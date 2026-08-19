---
name: prax-format
description: Generate and edit .prax course files — the plain-text eLearning format used by Praxity Studio
version: "3.0"
---

# .prax Format -- LLM Skill

## What is .prax

The `.prax` format is a plain-text course authoring format used by Praxity Studio, a desktop eLearning authoring tool. It uses augmented markdown: standard markdown with a small set of keywords (`as:`, `close:`, `var:`, `if:`) that transform plain elements into interactive learning blocks. A `.prax` file can be exported to SCORM, xAPI, or standalone HTML for deployment in any LMS. The format is designed to be human-readable, git-diffable, and LLM-friendly -- you can generate a complete interactive course in a single text file.


## File structure

A `.prax` file has two parts: optional YAML frontmatter and a body.

```
---
title: Course Title
lang: en
kicker: Module 1
design:
  palette: standard
  colorMode: light
---

Body content starts here...
```

The first `---` at byte position 0 opens YAML frontmatter. The next `---` closes it. All subsequent `---` tokens in the body are page breaks, not frontmatter.

`title` names the lesson/module. Optional `kicker` sets the short navigation label above it
(`kicker: Module 1`). Omit it or leave it blank to show no kicker.


## Frontmatter

Frontmatter controls course metadata and the design system. Only `title` and `lang` are needed for a minimal course. The `design` block is optional -- defaults produce a clean layout.

```yaml
---
title: Workplace Safety Fundamentals
lang: en
design:
  palette: standard
  colorMode: light
  accentHue: 220
  typography:
    fontDisplay: swap
    body:
      fontFamily: [Inter, "DM Sans", sans-serif]
      fontWeight: 400
      fontStyle: normal
    headings:
      fontFamily: ["DM Sans", Inter, sans-serif]
      fontWeight: 600
      fontStyle: normal
  density: comfortable
  navArchetype: scroll
  motionEntrance: fade
---
```

Key design fields: `palette` (see values below), `colorMode` (`light`, `dark`, `auto`), `accentHue` (0--360), semantic `color*` values (six-digit hex or opaque numeric `oklch()`), `density` (`compact`, `comfortable`, `spacious`), `blockSpacing` (`compact`, `default`, `spacious`), `navArchetype` (`sidebar`, `bottomBar`, `slides`, `scroll`, `minimal`, `none`), `dividerStyle` (`none`, `thin`, `gradient`, `wave`, `angle`, `curve`), `motionEntrance` (`none`, `fade`, `slide`, `scale`).

Valid palette values (all 13): `standard`, `minimal`, `universal`, `editorial`, `bold`, `cinematic`, `ocean`, `warm`, `dark`, `nature`, `pastel`, `corporate`, `playful`. Some map to the same underlying theme (e.g. `minimal` and `universal` share one base), but all 13 are valid author-facing names.


## Document structure

### Lessons and pages

`# H1` headings define **lessons** (top-level structural groupings). `---` page breaks define **pages** within a lesson.

```
# Module 1: Safety Basics

--- Introduction

Welcome to the first module.

--- Equipment Overview

Personal protective equipment includes...

# Module 2: Emergency Procedures

--- Fire Safety

In case of fire...
```

Content before the first `# H1` or `---` is implicit page 1 of an implicit first lesson.

### Page breaks

`---` on its own line starts a new page. An optional title follows on the same line. Parameters can follow on subsequent lines.

```
--- Safety Equipment
transition: slide-left
```

The page-break label appears only in navigation. An unlabelled visible page uses the plain text
of its first heading as its navigation label, or `Untitled` when it has no heading. The authored
heading remains visible; no fallback heading is synthesized.
A page break always closes all open containers. `hide: true` directly after a page break
hides the whole page.

Put the opening page's explicit navigation label and other fields in frontmatter because page 1
has no preceding break. Without an explicit label, its first heading provides the same fallback.
Optional narration uses an MP3 plus an optional WebVTT transcript:

```yaml
firstPage:
  title: Introduction
  narration: assets/introduction-a1b2c3.mp3
  captions: assets/introduction-a1b2c3.vtt
```

Put later-page fields directly after the page break:

```prax
--- Safety Equipment
narration: assets/safety-d4e5f6.mp3
captions: assets/safety-d4e5f6.vtt
```

Narration never autoplays or affects learner progress. A pronunciation lexicon may be stored in
lesson frontmatter or, with the same shape, in `course.yaml`:

```yaml
pronunciation:
  lexemes:
    - grapheme: WCAG
      alias: W C A G
      lang: en
```

Aliases match literal graphemes longest-first in the matching language. Lesson entries override
matching course entries; transcripts keep the original grapheme.

### Section dividers

Two dashes `--` on their own line create a styled section boundary within a page:

```
--
palette: warm
texture: grain
```

Without parameters, `--` is a plain visual divider.


## Block syntax

Four patterns create blocks:

1. **Bare markdown** -- paragraphs, headings, lists, code blocks, equations, tables work as standard markdown.
2. **`as:` transformer** -- placed after an element (`> text` then `as: note`) or before content (`as: col`).
3. **Content-block paths** -- a file path or URL on its own line creates a media block (extension determines type).
4. **Key-value parameters** -- `key: value` pairs on lines after a block, consumed greedily. Pipe-separated inline format also works: `width: large | alignment: center`.

Blank lines between parameters and content are optional.

### Universal parameters

These keys work on any block:

| Key | Effect |
|-----|--------|
| `name:` | Unique identifier for targeting (logic rules, anchor links) |
| `layout:` | Width override: `wide`, `full`, `breakout` |
| `hide:` | `true` to hide from rendered output |
| `reveal:` | Narration time (seconds or `MM:SS`), or `each` to reveal list items one at a time |
| `visible:` | Conditional visibility expression (e.g. `visible: passedQuiz`) |
| `entrance:` | Animation: `none`, `fade`, `slide`, `scale` |


## Content blocks

### Text

Bare paragraphs. No special syntax needed.

```
This is a paragraph of text with **bold** and *italic* formatting.
It can span multiple lines.
```

### Headings

```
## Section heading
### Sub-section heading
#### Minor heading
```

`# H1` defines a lesson boundary in a single-file course and is the visible H1 for
that page. In a multi-file course, use it as the page title. Published heading
levels match the authored `#` depth without an export-time shift.

### Image

A file path ending in an image extension (`.webp`, `.jpg`, `.jpeg`, `.png`, `.gif`, `.svg`, `.tiff`) on its own line:

```
/assets/hero-ppe.webp
alt: Workers wearing safety gear on a construction site
layout: full
caption: Workers inspect their protective equipment before a shift
```

| Key | Values | Default |
|-----|--------|---------|
| `alt:` | description (required for non-decorative) | -- |
| `decorative:` | `true`/`false` | `false` |
| `width:` | `small`, `medium`, `large` | -- |
| `alignment:` | `left`, `center`, `right` | `center` |
| `layout:` | `wide`, `full`, `breakout` | -- |
| `caption:` | custom string | -- |
| `effects:` | `none` or an effects map | -- |

### Video

A file path ending in `.mp4`, `.webm`, `.mov`, `.avi` or a URL from YouTube, Vimeo, Loom, Wistia:

```
/assets/safety-intro.mp4
caption: Safety walkthrough
transcript: /assets/safety-intro-transcript.txt
start: 12
end: 90
```

```
https://www.youtube.com/embed/aqz-KE-bpKQ
caption: Platform description and author
start: 12
end: 90
```

Keys: `title:`, `caption:`, `transcript:`, `start:`, `end:`. `start:` and `end:` are seconds and work for YouTube, Vimeo, local/direct video, and Mux. Loom embeds render but do not expose reliable playback timing control.

### Audio

A file path ending in `.mp3`, `.wav`, `.ogg`, `.m4a`, `.flac`, `.aac`:

```
/assets/podcast.mp3
title: Episode 12 -- Safety Culture
```

### Note (from blockquote)

A blockquote with `as: note` becomes a styled callout:

```
> Always wear PPE in designated zones.
as: note
title: Safety reminder
icon: alert-triangle
color: warning
style: filled
```

`color:` options are `accent` (default), `primary`, `secondary`, `success`, `warning`,
`error`, and `grey`. They publish as Note, Info, Info, Success, Warning, Warning, and Tip
respectively when `title:` is omitted.
`style: outline` is low emphasis; `filled` is high emphasis. Legacy `light` maps to low
emphasis and `shaded` to high emphasis. `title:` overrides the visible label. `icon:` accepts any
kebab-case [Tabler Icons](https://tabler.io/icons) outline icon name, such as `thinking-high` or
`sparkles`; use `none` for no icon, or omit it to derive the icon from the color. Exports embed only
the icons used by the document and do not require an icon CDN.
Every treatment keeps a tint and the same 1px semantic border. Legacy `emoji` values remain
parseable but published output uses the project icon.

### Quote

A plain blockquote (without `as: note`) renders as a styled quotation:

```
> The only way to do great work is to love what you do.
speaker: Steve Jobs
work: Stanford commencement address
sourceUrl: https://news.stanford.edu/stories/2005/06/youve-got-find-love-jobs-says
```

Keys: `speaker:` (person or organisation), `work:` (title, rendered as `<cite>`), and
`sourceUrl:` (visible link and blockquote `cite` URL). Quotes have one unfilled visual
treatment with a logical start rule and one hanging opening mark.
Legacy `attribution` maps to `speaker`; legacy `decorator`, `size`, and pull-quote `style`
values are accepted but ignored without dropping quote content.

### Code block

Standard markdown fenced code block with optional language:

````
```python
def calculate_risk(hazard_level, exposure):
    return hazard_level * exposure
```
````

### Equation

LaTeX math block delimited by `$$`:

```
$$
E = mc^2
$$
```

### Button (from link)

A standalone link with `as: button`:

```
[Download Safety Manual](https://example.com/manual.pdf)
as: button
variant: outline
openInNewTab: true
```

`variant:` options: `filled` (default), `outline`, `light`. `openInNewTab:` defaults to `false`.

### Bookmark (from link)

A standalone link with `as: bookmark` renders as a rich preview card:

```
[OSHA Safety Guidelines](https://www.osha.gov/safety-guidelines)
as: bookmark
```

The card prints the domain plus a detectable resource type, not the full URL. The full URL
remains available from the title and accessible link text.

### Table

Standard markdown pipe-delimited table (separator row `|---|---|` is optional):

```
| Quarter | Revenue | Costs |
| Q1      | 120     | 80    |
| Q2      | 150     | 90    |
```

Add `chart:` to render as a chart: `bar`, `line`, `scatter`, `area`, `radar`, `stacked`. Note: `pie` and `donut` are not supported. Additional keys: `xLabel:`, `yLabel:`, `title:`, `altText:`, `orientation:` (bar only), `sortOrder:`, `colors:`.

### Embed

An external URL with `as: embed`:

```
https://app.example.com/interactive-widget
as: embed
height: 400
```


## Container blocks

Containers group content. The `as:` key on a heading transforms it into a container. Consecutive headings at the same level with the same `as:` type are automatically grouped.

### Accordion

```
### What is a learning outcome?
as: accordion
style: contained

A learning outcome describes what a learner will be able to do
after completing instruction.

### What is a learning objective?

A learning objective is a specific, measurable statement.

### What is the difference?

Learning outcomes are broader; objectives are specific steps.
```

Only the first heading needs `as: accordion`. Subsequent headings at the same level are automatically grouped into the same accordion.

| Key | Values | Default |
|-----|--------|---------|
| `style:` | `default`, `contained`, `separated` | `default` |
| `allowMultipleOpen:` | `true`/`false` | `false` |

### Tabs

```
### Overview
as: tab

Overview content here...

### Details

Detailed content here...
```

Same grouping rules as accordion. Only the first heading needs `as: tab`.

### Sequence / Timeline

```
### 1. Design the grammar
as: sequence
variant: timeline

Define the syntax rules...

### 2. Implement the parser

Write the tokenizer...
```

| Key | Values | Default |
|-----|--------|---------|
| `variant:` | `numbered`, `timeline`, `plain` | `numbered` |
| `orientation:` | `vertical`, `horizontal` | `vertical` |

### Columns

Columns use standalone `as: col` markers (no heading required). Each `as: col` starts a new column. `close: col` ends the layout:

```
as: col
**Hazards** -- Chemical, biological, physical risks.

as: col
**Controls** -- Engineering and PPE-based solutions.

as: col
**Review** -- Annual audits and monthly checks.

close: col
```

Content after `close: col` returns to full-width flow. Column count is inferred from the number of `as: col` markers.

### Comparison

```
### Before
as: comparison
style: side-by-side

Manual incident logging with paper forms.

### After

Digital incident capture with real-time alerts.
```

Uses implicit heading grouping. Typically two items.

### Card

A grid of styled cards (`columns:` number, `style:` `outline`/`filled`/`elevated`). Requires `close: card`.

```
### Types of PPE
as: card
columns: 3
style: outline

### Head Protection
Hard hats and bump caps...

### Eye Protection
Safety goggles and face shields...

close: card
```

Use `card: back` for front/back behavior. Content before `card: back` is the front; content after it is the back.

```
## Safety Terms
as: card
layout: single
style: outline

### What is lockout/tagout?
Energy isolation before maintenance on energized equipment.

card: back
#### Answer
A formal energy-isolation procedure required before maintenance.

### When is a confined space permit required?

Before entering any space with limited entry/exit and potential hazardous atmosphere.

card: back
#### Answer
Permits are required when atmospheric or entrapment risks are present.

close: card
```

The card group heading is not part of the card face. Each item heading starts a new card and becomes its label/header. After `card: back`, use paragraph text or a lower-level heading for back-face content; a same-level item heading starts the next card.

### The `close:` keyword

`close:` ends containers whose boundaries cannot be inferred from headings alone:

| Required `close:` | Optional `close:` |
|--------------------|-------------------|
| `close: col` | `close: accordion` |
| `close: assessment-group` | `close: tab` |
| `close: card` | `close: sequence` |
| | `close: comparison` |

A `---` page break always closes all open containers automatically, even without explicit `close:`.


## Assessment blocks

Assessments use a heading for the question, `as:` for the type, and specialized answer syntax below.

### Shared assessment parameters

| Key | Effect | Default |
|-----|--------|---------|
| `points:` | Point value | `1` |
| `shuffle:` | Randomize option order | `false` |
| `attempts:` | Max attempts (0 = unlimited) | `1` |
| `required:` | Must complete to proceed | `false` |
| `scored:` | Include in scoring | `true` |

### Single choice (choose-one)

Use `(x)` for the correct answer and `( )` for incorrect options. The parentheses markers indicate single-selection (radio button) mode:

```
### Which PPE is required in construction zones?
as: choice
shuffle: true

( ) Safety glasses only
(x) Hard hat, safety glasses, and high-vis vest
( ) Steel-toed boots
( ) Whatever the supervisor says
```

**With per-option feedback** (the `feedback:` line must immediately follow its option):

```
### Which PPE is required?
as: choice

( ) Safety glasses only
feedback: Close, but you need more protection
(x) Hard hat, safety glasses, and high-vis vest
feedback: Correct -- all three are required
( ) Steel-toed boots
feedback: Important, but not sufficient alone
```

**With whole-question feedback** (placed after all options): use `correct:` and `incorrect:` for differentiated feedback based on overall correctness. These are distinct from per-option `feedback:` lines — `correct:` and `incorrect:` are not tied to any specific option and are stored as `data.correct`/`data.incorrect` on the block.

```
### Which PPE is required?
as: choice

( ) Safety glasses only
(x) Hard hat, safety glasses, and high-vis vest
( ) Steel-toed boots

correct: All three items are required in construction zones.
incorrect: Review the PPE requirements before continuing.
```

### Multiple choice (choose-many)

Use `[x]` for correct answers and `[ ]` for incorrect. The bracket markers indicate multi-selection (checkbox) mode:

```
### Select all WHMIS symbols. (select all that apply)
as: choice

[x] Flame symbol
[x] Exclamation mark
[ ] Smiley face
[x] Health hazard symbol
[ ] Skull and crossbones
```

IMPORTANT: Both single choice and multiple choice use `as: choice`. The marker syntax (`( )` vs `[ ]`) is what determines the selection mode. Do not mix marker types within the same question.

### Matching

```
### Match each hazard to its control measure.
as: match
shuffle: true

Chemical spill :: Containment kit
Electrical fault :: Lockout/tagout
Fall risk :: Harness and guardrail
Noise exposure :: Ear protection
```

Pairs are separated by `::`.

### Ordering

```
### Put the emergency response steps in order.
as: order

1. Assess the situation
2. Call emergency services
3. Administer first aid
4. Document the incident
```

The numbered list defines the correct order. The display is shuffled for the learner.

### Free response

```
### Describe three ways to improve workplace safety.
as: free-response
buttonLabel: Keep this in mind
description: Consider both physical and procedural improvements.
placeholder: Type your reflection
downloadAs: both
```

Keep instructions visible with `description:` and use `placeholder:` only for a brief hint. Bare
body text is legacy placeholder shorthand. `buttonLabel:` overrides the visible action; an ungraded
response otherwise uses **Complete reflection**. `downloadAs:` accepts `txt`, `docx`, or `both`.

### Fill in the blank

```
### Complete the safety procedure.
as: fill-blank

Before entering a confined space, workers must complete a
{permit} and test for {oxygen} levels. The minimum safe
oxygen concentration is {19.5}%.
```

`{word}` marks a blank whose correct answer is `word`. `____` marks an open blank with no
predefined answer. Both render inline without discarding the surrounding sentence. NOTE:
Inside `as: fill-blank` blocks, `{}` marks answer blanks, not variable interpolation.

### Rating / Likert

```
### How confident are you in using PPE correctly?
as: rating

1: Not at all confident
2: Slightly confident
3: Moderately confident
4: Very confident
5: Extremely confident
```

### Matrix

`as: matrix` renders a table-style Likert assessment. The heading is the matrix question, numbered lines define the scale, and list items define the statements.

```
### How confident are you?
as: matrix

1: Not at all confident
2: Slightly confident
3: Moderately confident
4: Very confident
5: Extremely confident

- Using PPE correctly
- Reading warning labels
- Following emergency procedures
```

Shorter scales are valid:

```
### How confident are you?
as: matrix

1: Not confident
2: Somewhat confident
3: Confident

- Using PPE correctly
- Reading warning labels
- Following emergency procedures
```

### Categorize

```
### Sort these items into the correct PPE category.
as: categorize
shuffle: true

Head Protection:
- Hard hat
- Bump cap

Eye Protection:
- Safety goggles
- Face shield
```

Category headers end with `:`. Items are list entries under each category.

### Hotspot

An image path with `as: hotspot`. Spots use semicolon-separated values:

```
/assets/fire-extinguisher-diagram.webp
as: hotspot
question: Select the handle.
alt: Fire extinguisher parts diagram
spot: Pressure gauge; 30%; 15%
spot: Handle; 45%; 25%; correct
spot: Nozzle; 60%; 40%
```

Format: `spot: label; x%; y%` with optional `; correct` flag.

### Assessment group

Wraps multiple assessments into a scored unit:

```
### Knowledge Check
as: assessment-group
passingScore: 80
mode: all
buttonLabel: Check all answers

### Which PPE is required?
as: choice

( ) Safety glasses only
(x) Hard hat and safety glasses

### Match hazards to controls
as: match

Chemical spill :: Containment kit
Fall risk :: Harness

close: assessment-group
```

| Key | Values | Default |
|-----|--------|---------|
| `passingScore:` | percentage (0--100) | none |
| `mode:` | `all`, `any` | `all` |
| `buttonLabel:` | text | `Check all` when ungraded; `Submit all` when graded |


## Interactive blocks

### Checklist

A task list with `as: checklist`:

```
- [ ] Wear hard hat
- [ ] Check safety harness
- [ ] Sign in at gate
as: checklist
required: true
```

| Key | Values | Default |
|-----|--------|---------|
| `required:` | `true`/`false` | `false` |
| `shuffle:` | `true`/`false` | `false` |

### Signature

```
### I confirm I have read and understood these procedures
as: signature
mode: draw
```

`mode:` options: `draw` (default), `type`.

## Inline formatting

Standard markdown: `**bold**`, `*italic*`, `~~strikethrough~~`, `==highlight==`, `` `inline code` ``, `[link text](url)`.

Variable interpolation: `{{userName}}` inserts variable values inline. Exception: inside `as: fill-blank`, single `{braces}` marks answer blanks — not interpolation.

Tooltips: `[PPE]{Personal Protective Equipment -- gear that protects.}` -- brackets for visible text, braces for the definition. Every defined term is also collected into a published glossary page; set `design.glossaryPage: false` to keep the tooltips without the page.

Doodle annotations: `@circle{important}`, `@underline.wavy{pay attention}`. Types: `@circle`, `@underline`, `@highlight`, `@arrow`, `@box`. Modifiers: `.wavy`, `.thick`, `.thin`, `.dashed`. Doodles can wrap glossary terms or links, for example `@highlight{Review [PPE]{Protective equipment}.}`.


## Variables and logic

### Variables

Short form: `var: attempts = 0`, `var: userName = "Learner"`, `var: passedQuiz = false`.

Extended form (for URL parameters etc.):

```
var: userName
type: text
source: url_parameter
param: name
default: Learner
```

### Conditional visibility

Any block can use `visible:` to show conditionally:

```
> Here is a hint.
as: note
color: warning
visible: attempts >= 3
```

### Logic rules

```
if: passedQuiz is false
and: attempts >= 2
then: show @remediation, @retry-help
then: set attempts = 0
```

Condition operators: `is`, `isNot`, `>`, `<`, `>=`, `<=`, `contains`, `isEmpty`, `isNotEmpty`, `isAnyOf`.

Action types: `show @name, @other`, `hide @name, @other`, `jump @page`, `set variable = value`, `add variable + n`, `subtract variable - n`, `require @name, @other`, `disableCompletion`. `when:` is accepted as a compatibility alias for `if:`.

`and:` and `or:` chain conditions but cannot be mixed in one rule.


## Escaping

Prefix a line with `\` to prevent the parser from interpreting it as a keyword:

```
\as: this is literal text, not a block declaration
\--- this is literal text, not a page break
```


## Complete example

A working mini-course exercising frontmatter, headings, images, accordion, quiz with feedback, and page breaks:

```
---
title: Workplace Safety Fundamentals
lang: en
design:
  palette: standard
  colorMode: light
  accentHue: 220
---

--- Introduction

## Welcome to Safety Training

Every worker deserves to go home safe. This course covers
PPE selection, hazard identification, and emergency procedures.

/assets/hero-ppe.webp
alt: Workers wearing safety gear on a construction site
layout: full

> This course meets OSHA 10-hour training requirements.
as: note
color: accent

--- Equipment Overview

### Head Protection
as: accordion
style: contained

Hard hats protect against falling objects. Inspect for cracks before use.

### Eye Protection

Safety goggles prevent chemical splashes and flying debris.

### Hand Protection

Nitrile gloves protect against chemicals. Cut-resistant gloves
are required when handling sheet metal.

--

## Knowledge Check

### Which PPE is required in construction zones?
as: choice
shuffle: true

( ) Safety glasses only
feedback: Close, but you need more protection.
(x) Hard hat, safety glasses, and high-vis vest
feedback: Correct! All three are required.
( ) Steel-toed boots only
feedback: Important, but not sufficient alone.

### Put the emergency response steps in order.
as: order

1. Assess the situation
2. Call emergency services
3. Administer first aid
4. Document the incident

--- Summary

- [ ] I have read all safety procedures
- [ ] I can identify required PPE for my work area
as: checklist
required: true

### I confirm I have completed this training
as: signature
mode: draw
```


## Syntax quick reference

| What you want | Syntax |
|---------------|--------|
| Page break | `---` or `--- Page Title` |
| Section divider | `--` |
| Lesson boundary | `# Lesson Title` |
| Image | `/path/to/image.webp` + `alt: description` |
| Video | `/path/to/video.mp4` or YouTube URL, optionally with `start:` / `end:` |
| Note callout | `> text` + `as: note` |
| Quote | `> text` + `attribution: Author` |
| Button | `[text](url)` + `as: button` |
| Accordion | `### Title` + `as: accordion` |
| Tabs | `### Title` + `as: tab` |
| Columns | `as: col` ... `close: col` |
| Single choice | `### Q` + `as: choice` + `(x)`/`( )` options |
| Multiple choice | `### Q` + `as: choice` + `[x]`/`[ ]` options |
| Matching | `### Q` + `as: match` + `left :: right` pairs |
| Ordering | `### Q` + `as: order` + numbered list |
| Free response | `### Q` + `as: free-response` |
| Fill in blank | `### Q` + `as: fill-blank` + `{answer}` or `____` blanks |
| Checklist | `- [ ] items` + `as: checklist` |
| Variable | `var: name = value` |
| Logic rule | `if: condition` + `then: action` (`when:` is an alias) |
| Escape a keyword | `\keyword: treated as text` |


## Multi-file courses

A single `.prax` file works for simple courses. For larger projects, use a **multi-file course** with a `course.yaml` manifest:

```
safety-training/
  course.yaml
  assets/
  intro.prax
  hazards.prax
  emergency.prax
```

The manifest defines lesson order, course metadata, and shared design:

```yaml
title: Safety Training Fundamentals
locale: en
lessons:
  - intro.prax
  - hazards.prax
  - emergency.prax
design:
  palette: ocean
  navArchetype: sidebar
```

Each `.prax` file is a standalone lesson. Settings cascade: `course.yaml` design applies to all lessons unless a lesson's frontmatter overrides it. See `reference/course-manifest.md` for the full schema.


## When to load sub-skills

This root skill covers the most common blocks and patterns. For advanced usage, load these specialized sub-skills:

| Sub-skill | Load when... |
|-----------|-------------|
| `assessments.md` | Building complex assessments: matrix questions, hotspot authoring details, assessment groups with passing scores, detailed feedback strategies |
| `containers.md` | Advanced container nesting, card layouts, flip cards (`card: back`), comparison variants, sequence orientations |
| `design.md` | Fine-tuning the design system: custom fonts, color tokens, image effects, section palettes, background textures, motion and animation |
| `validation.md` | Checking a `.prax` file for errors: required fields (alt text), assessment structure, container balance, frontmatter schema |
