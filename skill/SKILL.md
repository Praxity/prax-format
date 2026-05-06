---
name: prax-format
description: Generate and edit .prax course files — the plain-text eLearning format used by Praxity
version: "3.0"
---

# .prax Format -- LLM Skill

## What is .prax

The `.prax` format is a plain-text course authoring format used by Praxity, a desktop eLearning authoring tool. It uses augmented markdown: standard markdown with a small set of keywords (`as:`, `close:`, `var:`, `when:`) that transform plain elements into interactive learning blocks. A `.prax` file can be exported to SCORM, xAPI, or standalone HTML for deployment in any LMS. The format is designed to be human-readable, git-diffable, and LLM-friendly -- you can generate a complete interactive course in a single text file.


## File structure

A `.prax` file has two parts: optional YAML frontmatter and a body.

```
---
title: Course Title
lang: en
design:
  palette: standard
  colorMode: light
---

Body content starts here...
```

The first `---` at byte position 0 opens YAML frontmatter. The next `---` closes it. All subsequent `---` tokens in the body are page breaks, not frontmatter.


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
  bodyFont: Inter
  headingFont: DM Sans
  density: comfortable
  navArchetype: scroll
  motionEntrance: fade
---
```

Key design fields: `palette` (see values below), `colorMode` (`light`, `dark`, `auto`), `accentHue` (0--360), `density` (`compact`, `comfortable`, `spacious`), `navArchetype` (`sidebar`, `bottomBar`, `slides`, `scroll`, `minimal`, `none`), `dividerStyle` (`none`, `thin`, `gradient`, `wave`, `angle`, `curve`), `motionEntrance` (`none`, `fade`, `slide`, `scale`).

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

The page title appears in navigation but is NOT rendered as a visible heading unless `title: show` is set. A page break always closes all open containers.

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
4. **Key-value parameters** -- `key: value` pairs on lines after a block, consumed greedily. Pipe-separated inline format also works: `treatment: duotone | size: full-width`.

Blank lines between parameters and content are optional.

### Universal parameters

These keys work on any block:

| Key | Effect |
|-----|--------|
| `name:` | Unique identifier for targeting (logic rules, anchor links) |
| `layout:` | Width override: `wide`, `full`, `breakout` |
| `hide:` | `true` to hide from rendered output |
| `reveal:` | Progressive reveal: number (step), `each` (list stagger), `true` (auto), or variable name |
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

`# H1` is reserved for lesson boundaries (see Document Structure). Do not use H1 for content headings.

### Image

A file path ending in an image extension (`.webp`, `.jpg`, `.jpeg`, `.png`, `.gif`, `.svg`, `.tiff`) on its own line:

```
/assets/hero-ppe.webp
alt: Workers wearing safety gear on a construction site
size: full-width
caption: show
```

| Key | Values | Default |
|-----|--------|---------|
| `alt:` | description (required for non-decorative) | -- |
| `decorative:` | `true`/`false` | `false` |
| `size:` | `small`, `medium`, `large`, `full-width` | `large` |
| `x:` | `left`, `center`, `right` | `center` |
| `treatment:` | `none`, `duotone`, `grayscale`, `sepia`, `blur` | `none` |
| `caption:` | `show`, `hide`, or custom string | `hide` |
| `order:` | `content`, `background` | `content` |

### Video

A file path ending in `.mp4`, `.webm`, `.mov`, `.avi` or a URL from YouTube, Vimeo, Loom, Wistia:

```
/assets/safety-intro.mp4
subtitles: /assets/safety-intro-en.srt
```

```
https://www.youtube.com/embed/aqz-KE-bpKQ
```

Keys: `subtitles:` (path to `.srt`/`.vtt`), `transcript:` (file path), `autoplay:` (`true`/`false`, default `false`).

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
color: warning
style: filled
```

`color:` options: `accent` (default), `primary`, `success`, `warning`, `error`, `grey`. `style:` options: `filled` (default), `outline`.

### Quote

A plain blockquote (without `as: note`) renders as a styled quotation:

```
> The only way to do great work is to love what you do.
attribution: Steve Jobs
decorator: quotation-marks
```

Keys: `attribution:` (string), `size:` (`default`, `large`, `very-large`), `decorator:` (`line-left`, `quotation-marks`, `cinematic`).

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

### Table

Standard markdown pipe-delimited table (separator row `|---|---|` is optional):

```
| Quarter | Revenue | Costs |
| Q1      | 120     | 80    |
| Q2      | 150     | 90    |
```

Add `chart:` to render as a chart: `bar`, `line`, `scatter`, `area`, `radar`, `stacked`. Note: `pie` and `donut` are not supported. Additional keys: `xLabel:`, `yLabel:`, `title:`, `altText:`, `orientation:` (bar only), `sortOrder:`, `colorScheme:`.

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

### Flashcard

With headings (first paragraph = front, rest = back):

```
### What is PPE?
as: flashcard

Personal Protective Equipment -- protective clothing and gear.
close: flashcard
```

Without headings, use standalone `as: flashcard` ... `close: flashcard`. First paragraph = front face, subsequent content = back face.

### Card

A grid of styled cards (`columns:` number, `style:` `outline`/`filled`/`elevated`):

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

### The `close:` keyword

`close:` ends containers whose boundaries cannot be inferred from headings alone:

| Required `close:` | Optional `close:` |
|--------------------|-------------------|
| `close: col` | `close: accordion` |
| `close: assessment-group` | `close: tab` |
| `close: flashcard` (without heading) | `close: sequence` |
| `close: card` | `close: comparison` |

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

**With whole-question feedback** (placed after all options): use `feedback:` for a single message, or `correct:` and `incorrect:` for differentiated feedback.

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

Consider both physical and procedural improvements.
```

Body text serves as placeholder/helper text.

### Fill in the blank

```
### Complete the safety procedure.
as: fill-blank

Before entering a confined space, workers must complete a
{permit} and test for {oxygen} levels. The minimum safe
oxygen concentration is {19.5}%.
```

`{word}` marks blanks. The text inside braces is the correct answer. NOTE: Inside `as: fill-blank` blocks, `{}` marks answer blanks, not variable interpolation.

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

### Discussion / Poll / AI Chat

```
### Share your experience
as: discuss
```

Other interactive `as:` values: `qa` (Q&A format), `poll`, `contribute` (share prompt), `ai-chat` (with `topic:` and `prompt:` keys).

**Note:** `discuss`, `qa`, `poll`, `contribute`, and `ai-chat` require Cloud publish to function. They will not render in standalone SCORM or HTML exports.


## Inline formatting

Standard markdown: `**bold**`, `*italic*`, `~~strikethrough~~`, `==highlight==`, `` `inline code` ``, `[link text](url)`.

Variable interpolation: `{{userName}}` inserts variable values inline. Exception: inside `as: fill-blank`, single `{braces}` marks answer blanks — not interpolation.

Tooltips: `[PPE]{Personal Protective Equipment -- gear that protects.}` -- brackets for visible text, braces for tooltip.

Doodle annotations: `@circle{important}`, `@underline.wavy{pay attention}`. Types: `@circle`, `@underline`, `@highlight`, `@arrow`, `@box`. Modifiers: `.wavy`, `.thick`, `.thin`, `.dashed`.


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
when: passedQuiz is false
and: attempts >= 2
then: show @remediation
then: set attempts = 0
```

Condition operators: `is`, `isNot`, `>`, `<`, `>=`, `<=`, `contains`, `isEmpty`, `isNotEmpty`, `isAnyOf`.

Action types: `show @name`, `hide @name`, `jump @page`, `set variable = value`, `add variable + n`, `subtract variable - n`, `require @name`, `disableCompletion`.

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
size: full-width

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
| Video | `/path/to/video.mp4` or YouTube URL |
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
| Fill in blank | `### Q` + `as: fill-blank` + `{answer}` blanks |
| Checklist | `- [ ] items` + `as: checklist` |
| Variable | `var: name = value` |
| Logic rule | `when: condition` + `then: action` |
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
badge: true
```

Each `.prax` file is a standalone lesson. Settings cascade: `course.yaml` design applies to all lessons unless a lesson's frontmatter overrides it. See `reference/course-manifest.md` for the full schema.


## When to load sub-skills

This root skill covers the most common blocks and patterns. For advanced usage, load these specialized sub-skills:

| Sub-skill | Load when... |
|-----------|-------------|
| `assessments.md` | Building complex assessments: matrix questions, hotspot authoring details, assessment groups with passing scores, detailed feedback strategies |
| `containers.md` | Advanced container nesting, card layouts, flashcard collections, comparison variants, sequence orientations |
| `design.md` | Fine-tuning the design system: custom fonts, color tokens, image effects, section palettes, background textures, motion and animation |
| `validation.md` | Checking a `.prax` file for errors: required fields (alt text), assessment structure, container balance, frontmatter schema |
