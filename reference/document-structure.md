# Document Structure

## Frontmatter

Frontmatter is optional YAML at the top of the file.

```prax
---
id: lesson-8f565705-3918-4ef7-9710-3933e3485ad8
title: Workplace Safety 101
lang: en
kicker: Module 1
design:
  palette: standard
  colorMode: light
---
```

Rules:

- Must start at the first line.
- Uses `---` opening and closing fences.
- Body content starts after closing fence.

`title` is the lesson or module name. `kicker` controls the short label shown above that
title in learner navigation:

```yaml
kicker: Module 1
```

When `kicker` is omitted or blank, no kicker is shown. In a multi-file course, `course.yaml`
supplies the overall course title and each lesson's `title` labels its navigation link.
Lesson kickers are omitted from the multi-file course outline.

`lang` sets the lesson language. It takes precedence over the course `locale`, which
takes precedence over the workspace `defaultLocale`. With none of them set, the
language is `en`. Each published page or module uses its lesson's language for its
HTML `lang` attribute and its learner controls. In a mixed-language course, a lesson's
`lang` applies only to that lesson; the next lesson falls back to the course language.

## H1 heading

`#` creates an ordinary level-one heading. It accepts the same heading parameters, `as:` transformations, and Studio narration handling as other heading levels. It does not create a lesson, module, or page boundary.

```prax
# Safety essentials
width: narrow
display: chapter
kicker: Before you begin
```

Files and their frontmatter define lessons or modules; `course.yaml` organizes a multi-file course. Within a file, only an explicit `---` page break starts another page. Keep the authored heading level when editing or saving, including an opening `##`; Studio does not promote every opening heading to H1.

## Lists

A line indented by at least two spaces beyond the list marker's indentation continues the item above it. A blank line followed by an indented line starts a new paragraph in the same item. Unindented text ends the list.
Recognized parameter lines, including indented `as:` lines, stay outside the list item. Other indented lines can contain colons and still continue the item.

## Pages and navigation labels

Pages are separated with `---` on its own line. Text after the dashes labels the new page in navigation; it is not rendered as a heading. Use an authored `#` heading when the page needs a visible title.

```prax
# Page One
Content on page one.

--- Introduction

# Page Two
Content on page two.
```

Parser behavior:

- `---` creates a `pageBreak` block.
- `--- Label` creates a `pageBreak` block with `title: "Label"`.
- An unlabelled visible page uses the plain text of its first heading as its navigation label; it appears as `Untitled` only when it has no heading.
- `hide: true` immediately after a page break hides that whole page.
- A page break also closes all open containers and implicit groups.

```prax
--- Internal navigation label
hide: true

# Visible page title
```

Page 1 has no preceding page break, so its explicit navigation label and page defaults live in
frontmatter. Without an explicit label, its first heading provides the same fallback.

```yaml
---
title: Example lesson
firstPage:
  title: Introduction
---
```

For module playback, `firstPage.deckStop` and page-break `deckStop` pause narration after that slide. See [module deck settings](module-deck.md).

## Named page styles and corner artwork

Define reusable page styles in frontmatter, then select one with `pageStyle` after a
page break or on the page's opening H1:

```prax
---
title: Example lesson
pageStyles:
  illustrated:
    cornerImage: assets/corner-wash.png
  plain:
    cornerImage: none
---

# Welcome
pageStyle: illustrated

--- Next steps
pageStyle: illustrated

# Next steps

--- Summary
pageStyle: plain

# Summary
```

A page-break `pageStyle` takes precedence over the opening H1's `pageStyle`. Only
an H1 that is the page’s first top-level block can set its page style; later or
nested headings do not. A heading's ordinary `style` still styles that heading.
The older frontmatter `styles` and page-break `style` spellings remain supported
for existing source. When both dictionaries are present, `pageStyles` takes precedence.
For an opening page without a page break, `firstPage.pageStyle` in frontmatter
can select the style instead of the H1 parameter.

Each named style can set these presentation properties. Defaults below apply to
a new custom style; a definition named after a built-in style inherits that style’s defaults.

| Property | Values | Default |
|---|---|---|
| `cornerImage` | Asset path, or `none` | No artwork |
| `background` | `paper`, `surface`, `accent`, `ink` | `paper` |
| `measure` | `narrow`, `default`, `wide`, `full` | `default` |
| `align` | `start`, `center` | `center` |
| `scale` | `default`, `display` | `default` |
| `chrome` | `default`, `minimal` | `default` |
| `accentRule` | `true`, `false` | `false` |
| `mediaPlacement` | `stacked`, `left`, `right` | `stacked` |

`background` selects theme colours; `cornerImage` adds decorative artwork independently.
`align` controls vertical placement, and `measure` controls the content width.
`accentRule` adds the accent border used by the built-in `section` style.

`cornerImage` is optional and off by default. Use artwork designed for cropped
upper-left and lower-right corners. Keep meaningful illustrations in ordinary
image blocks with alt text. Styles can name different artwork files; reuse the
same style on as many pages as needed. A style with `cornerImage: none` has no
corner artwork.

Slide mode reserves space beside the content automatically, using the slide's
available width after docked panels. Narrow slides, and slides containing full or
breakout-width blocks, show a smaller wash after the content instead. Other
presentation layouts retain the setting without displaying the artwork.
There are no authored crop, opacity, or breakpoint controls. The image is omitted
in dark mode, forced colours, and print, and adds no transcript, narration, or
navigation content.

Local artwork is included in preview and exported packages through the ordinary
asset pipeline. Keep the file with the course's other assets.

## Stable content IDs

Stable IDs connect learner progress to the same lesson, page, and interactive block after
source edits or preview regeneration. Preserve existing IDs; do not copy one onto another item
or change it to rename content.

An ID is 1–128 ASCII letters, numbers, underscores, or hyphens and must start with a letter or
number. IDs must be unique across the course. Studio writes a missing lesson `id` into
frontmatter. It stores generated page and stateful block IDs in the project's
`.praxity/content-identity.json` file, so they do not appear in the editor or `.prax` source.
Stateful blocks include accordion, assessment, assessment group, button, card, checklist,
image comparison, labeled graphic, rating, sequence, signature, and tabs.

For saved labeled-graphic data with `reveal: sequential`, learners explore numbered
pins in order. A visible, localized instruction explains that later pins become
available after the preceding pin is revealed. Future pins are disabled; the next
pin and all previously revealed pins remain available. Selecting a revealed pin
reopens or closes its description without reducing progress. Saved progress restores
the revealed sequence and active description. Without JavaScript, an ordered list
shows every title and description. Unrestricted `reveal: click` keeps every pin
available, and omitted reveal mode still shows all labels. No source syntax or
persisted data fields change.

Explicit IDs remain supported. Use `firstPage.id` for the first page, `id` after a page break
for later pages, or a block's `id` parameter:

```prax
--- Next steps
id: next-steps

### Confirm completion
as: signature
id: confirm-completion
```

Use `name:` when authoring a block anchor for logic. Generated runtime identities do not need
an authored name or ID.

When opening older Studio source, Studio records canonical generated page and block UUIDs in
project metadata before removing their source rows. It preserves referenced IDs, custom IDs,
and noncanonical forms. Older source without IDs remains valid.

Keep `.praxity/content-identity.json` when copying or backing up a project. Copying a clean
`.prax` alone into another project creates new generated page and block identities. Explicit
source IDs travel with the file. Legacy source still carrying generated UUIDs retains those
values during migration. Studio's rename commands preserve generated identities.
Unsaved previews do not replace saved identity records.

Studio preserves identities when it can match content unambiguously. Ambiguous duplicate or
external edits receive new IDs rather than inheriting another block's saved learner state.

Studio narration is a project layer stored in `narration.yaml`, not grammar syntax. Studio owns
that machine-managed sidecar and links its scripts and audio to the visible page title and logical
blocks. Authors and agents should edit the `.prax` source for visible content, then use Studio to
edit narration. The CLI reads the same sidecar and bundles referenced MP3 and optional WebVTT
assets. In the default page runtime, narration never autoplays or affects completion, scoring, content access, or navigation. The experimental [module-deck compiler option](module-deck.md) adds user-started continuous playback and slide navigation. Its knowledge-check gates remain independent of listening time.

Cards, sequences, accordions, tabs, and dialogues generate separate narration clips for each item,
with a separate clip for any container title or instructions. Two-sided cards have separate
front and back clips, in that order; each face has its own narration settings. Nested containers retain their
own item boundaries. Preview and export play the clips in authored order. This keeps a long
container from consuming Soniox's two-minute limit in one request; an individual item still
needs to fit that limit. Existing whole-container recordings remain playable. Regenerating a
derived recording replaces it with item clips only after the full replacement batch succeeds;
explicit custom whole-container scripts remain intact.

Derived narration puts each card, tab, accordion or sequence item's label and child headings
in separate paragraphs from the body. If a container title repeats an item label, the retained
text still acts as a heading. Whole-container narration keeps the paragraph boundaries of
nested child scripts, including nested containers. Assessment stems and descriptions keep
those boundaries too. When a heading has following text and ends without punctuation, Studio
adds `[pause]` after it for speech. Transcripts and captions omit that cue.
A heading that ends in punctuation needs no added pause. A heading-only segment keeps its
existing script because the next clip already has a separate recording boundary. Source
anchors and segment IDs stay unchanged. Existing generated clips whose speech gains a pause
remain linked but become stale until regenerated. Custom scripts and recordings remain intact.

A dialogue has one clip for its caption, if any, and one per turn. Each turn uses its speaker's voice from the local `speaker:` declaration or the course cast unless the segment sets its own voice. Speaker names are shown, never spoken. A clip follows its turn when turns move; editing the words or the speaker key unlinks it. Give two otherwise identical dialogues different `name:` values to keep their clips apart.

For a [branching dialogue](../examples/patterns/dialogue-branching.prax), use a scored choice and logic that reveals the committed answer's branch. The standard-page playlist is computed at export and stored in `praxity-config`. A `hidden` logic-controlled wrapper, an ancestor wrapper, or a `.praxity-section` controls eligibility. A block without its own wrapper stays eligible unless an ancestor or section is hidden; inactive tabs, collapsed accordions and other card faces stay eligible and are revealed when selected. Hidden words never play.

The standard page uses authored stop positions at assessment and media boundaries, including boundaries with no audio. An active stop applies to the nearest eligible predecessor. A stop is active only when its originating block, every logic-controlled block or section ancestor, and any conditional owner are shown. A terminal stop stays pending if no branch is eligible, so Play enters a branch revealed after submission. Play then moves strictly forward in authored order; reach an earlier branch by seeking or selecting it. Public `praxity:assessment-response` and `praxity:assessment-group-complete` events pause narration after submission. Private assessments emit no response event; their option selection and submit use indistinguishable `praxity:block-interaction` events, so private submission alone does not guarantee a pause. Deck media keeps its existing `pauseAfter`; authored stops apply only to the standard page. Decks do not support branching.

If child words of a card, tab or accordion item become conditional, its source anchor changes. The old whole-item recording becomes unlinked; new item segments have missing audio until the author regenerates them. They are not automatically marked stale.

Generated narration for single- and multiple-choice questions reads the prompt, description,
instructions, and visible answer options in authored order, then pauses for the learner.
It excludes correctness markers, scoring, answer keys, and feedback. Custom narration scripts
override this generated text. After a generated script changes, regenerate its audio and timings;
existing clips are not rewritten automatically.

Studio keeps one authored narration script for generation and playback metadata. Recognized Soniox emotion and delivery cues such as `[sincerely]`, `[delighted]`, `[whispering]`, and `[long pause]` remain in that script and are sent for generation. Learner transcripts, captions, and reading labels omit these nonspoken cues. Human sound captions such as `[laughs]`, `[sighs]`, and `[coughs]` remain visible. Ordinary bracketed text, citations, and Markdown links are not removed as delivery cues.

To write a cue as literal text, escape its opening bracket: `\[warm]`. The transcript shows `[warm]`, while Soniox receives and speaks `warm` without brackets. [Soniox documents bracketed tags](https://soniox.com/docs/tts/concepts/emotion-and-tone) but no literal-tag escape, so Studio removes the brackets before synthesis.

Recording freshness uses the complete original generation script, including its cues. Changing only a cue therefore marks the existing recording out of date. This does not require a separate learner-transcript script.

Removing a configured voice marks a recording out of date when generation would now use a different provider default voice. A recording made with that provider default stays current.

Narration scripts also supply the reading text in the current slide’s transcript. In Studio's script
field, use `**bold**`, `*italic*`, `#` through `###` headings, `-` bullet items, and `1.` numbered
items. Put each heading or list item on its own line, and separate paragraphs with blank lines.
Indent list items by two spaces per level to nest them. Studio also derives one script line per
item from prose lists in block content, including numbered and nested lists. Transcript headings
keep the surrounding text size. Raw HTML and other inline features are literal text, not
executable markup.

Within an assessment stem or description, derived narration reads repeated child text in
authored order. Separate numbered lists in those children remain separate in the transcript,
so each list keeps its own starting number. PDF narration notes keep nested lists intact.

Formatting stays in the existing sidecar `script` field. Speech receives the same words with
formatting removed and paragraph breaks between headings, paragraphs, and list items. Studio
adds no sentence punctuation and keeps the original segment IDs and recording boundaries.
Bold and italic are visual formatting, not Soniox emphasis instructions; supported bracketed
voice cues such as `[warm]` and `[pause]` still reach the provider. Formatting-only changes can
reuse a recording when its saved spoken text matches. Older recordings made with literal
asterisk emphasis remain stale until regenerated. No recording is regenerated automatically.

An optional pronunciation lexicon maps exact written terms to spoken aliases during online
narration generation. It does not change learner-visible text:

```yaml
pronunciation:
  lexemes:
    - grapheme: WCAG
      alias: W C A G
      lang: en
```

Matches are literal, case-sensitive, language-specific, and longest-first. Use separate entries
for capitalization or inflection variants. Course manifests may use the same
`pronunciation.lexemes` shape; lesson-frontmatter entries override matching course entries.

## Headings

Heading levels drive hierarchy and structure.

- `#` level-one content heading, recommended for a visible page title
- `##` section heading
- `###` section/item heading
- `####` subsection heading
- `#####` level-five heading
- `######` level-six heading

Published HTML preserves all six authored levels: `#` → `h1`, `##` → `h2`,
`###` → `h3`, `####` → `h4`, `#####` → `h5`, and `######` → `h6`. Course and lesson
labels stay in navigation and do not shift content headings down. Studio adds no
heading when a page has no authored H1. Choose each level for its place in the
content hierarchy.

```prax
# Module Title
## Incident Response Basics
### Immediate Actions
#### Notify Stakeholders
##### Notify the response team
###### Confirm receipt
```

## `as:` transformation

`as:` converts standard markdown structure into a specific block type.

```prax
### Checkpoint Question
as: choice

(x) Correct
( ) Incorrect
```

Common uses:

- `as: choice`, `as: match`, `as: order`, `as: free-response`, `as: rating`
- `as: accordion`, `as: tab`, `as: sequence`, `as: comparison`
- `as: stats`, `as: signature`, `as: checklist`

Keep a block's parameter lines together. Studio skips blank lines before the
first parameter, then stops reading parameters at the next blank line. Structural
commands such as `close:`, `var:`, and `card: back` end parameter collection,
even without a separating blank line. A blank line before a standalone
`as: col`, `as: card`, or `as: flashcard` starts a new container.

## Section divider vs page break

- `--` is a divider within a page.
- `---` is a page break between pages.

```prax
## Same Page
Intro text.

--

Additional section on same page.

---

## New Page
```

Add `palette:` after `--` to start a coloured section band. Valid section
palettes are `light`, `dark`, `accent`, `warm`, `cool`, and `growth`. Explicit
bands work with `uniform`, `alternate`, and `manual` section rhythm. An
unmarked opening section keeps the page background. A plain `--` remains a
visual divider and does not select a palette. An H2 immediately after a palette
divider stays in that band with its content.

## Lessons and grouping

Each `.prax` file supplies lesson or module content and frontmatter metadata. Use `course.yaml` to organize multiple files. Within a file, use `---` for pages and headings for content hierarchy. A heading never creates another lesson.

## Universal parameters

Use `style:` for a block's visual treatment and `orientation:` for its direction.
Values are specific to the block; a style supported by one block need not apply to
another. Button, tabs, and sequence accept the older `variant:` spelling for
compatibility, but new source uses `style:`. For sequences, `none` replaces the
older `plain` label. Studio writes the canonical spelling when serializing.

Generic `reveal:` is retired. Existing values remain readable and preserve the
content, which is displayed normally. Use a sequence, accordion, or card for
learner-controlled disclosure, or logic for visibility based on a condition.
Assessment `feedbackMode: reveal` and block-specific disclosure are unaffected.

All manifest blocks support the following parameters:

| Parameter | Type | Valid values | Description |
|---|---|---|---|
| `width` | enum | `narrow \| wide \| full \| breakout` | Overrides the default content width for this block |
| `name` | string | any | Assigns a name to the block for cross-referencing. Used in logic rules (`then: show @myBlock`), assessment-group scoring, and anchor links. Use `camelCase` with no spaces, such as `name: safetyTip`. Avoid colons, quotes, and special characters. |
| `hide` | boolean | `true \| false` | Hides the block from rendered output. The block is preserved in the grammar and can be shown later via logic rules (`then: show @name`). |
| `visible` | condition expression | always | Conditional visibility based on variable state. Example: `visible: score >= 80`. The block renders only when the condition is true. |
| `entrance` | enum | `fade \| slide \| scale \| none` | Block entrance animation; overrides course-level `motionEntrance`. |
| `entranceDuration` | CSS duration | `250ms` | Duration of the entrance animation. |

A rule can read a named assessment's result with `if: block:spillResponse.result is "correct"`.
Use `then: show @correctFollowUp` to reveal a card with `name: correctFollowUp` and
`hide: true`. Studio preserves the field reference and the card parameters when saving.

## Continue gates

Use a logic rule to block forward navigation while a prerequisite remains unmet.
Give the learner an actionable reason with `because`:

```prax
if: block:safetyCheck.result isNot "correct"
then: disableCompletion because "Submit a correct answer before continuing."
```

The optional reason must be a JSON double-quoted string. Escape embedded quotes
as `\"`, backslashes as `\\`, and control characters as `\n` or `\t`. Studio
preserves the reason when saving. A malformed reason produces a parser warning
and Studio drops that action; other actions and blocks still parse.

While the condition holds, the rule gates Next, forward keyboard and swipe
navigation, forward jumps, and completion through forward navigation. Backward
navigation remains available. Continue stays visible and unavailable, with the
reason beside it and linked to it for assistive technology. Once the condition
clears, Continue becomes available.

Plain `then: disableCompletion` remains valid. Studio recommends a reason when
it is absent or blank. The localized fallback says that the next page is
unavailable but cannot explain the authored prerequisite.

See the [complete Continue gate example](../examples/patterns/continue-gate.prax).

## End the lesson

`__end_lesson__` is a reserved jump target. A rule that jumps to it finishes the
course when the learner moves forward from that page:

```prax
if: block:finalCheck.result is "correct"
then: jump @__end_lesson__
```

It runs only when the learner selects Next or Continue, and only after required
questions are answered and no Continue gate blocks the page. The learner stays
on the page. In a SCORM package on Pages, finishing saves the learner's progress,
marks the course complete unless the learner has failed it, and closes the LMS
session. After that the page no longer reports to the LMS. This is the only way
a published SCORM course closes its session; leaving the page or closing the
window saves progress without closing it. Guided slides have no finish action, so the LMS ends the session
when the learner closes the course.

## Block widths

`width: narrow` centers a block in a measure capped at `45ch`, using the surrounding body font. It keeps the full available width in a narrower parent or viewport and preserves text alignment. Use it for short passages such as notes and quotes on pages or slides. Omit `width` for the normal content width. Wider blocks stay centered in the available content area and retain a side gutter, including when a navigation panel reduces that area. `width: full` fills this usable area; section backgrounds and decorative artwork can still extend to its edges. Cards can combine a presentation such as `layout: slides` with `width: narrow`.

In module decks, `width: breakout` extends beyond the readable text column, while `width: full` fills the slide area with a side gutter. This also applies inside sections. Both stop at an open outline or transcript panel. At narrow viewports, they remain at least as wide as the text column and fit the slide without horizontal scrolling.

```prax
> Pause to consider how this applies to your work.
as: note
width: narrow
```

`width` is the universal parameter in the manifest. Narration lives in the project sidecar, not block parameters. The others (`name`, `hide`, `visible`, `entrance`, `entranceDuration`) are runtime workflow metadata recognized by the published-output viewer.

## Escaping reserved lines

If text needs to start with a reserved key (`as:`, `close:`, `if:`, `when:`), escape it with `\`.

```prax
\as: this is literal text, not a block transform
```

A line prefix does not remove a second inline escape. For example,
`\*stars\*` prints `*stars*` without emphasis. Unknown escapes such as `\q`
remain literal. See [inline escaping](inline-formatting.md#escaping) for the
reserved punctuation and field delimiters.

## Edge cases

- Malformed YAML frontmatter produces a warning at the affected line. The body still parses, and Studio leaves the original frontmatter unchanged until the YAML is fixed. Design changes remain pending while it is invalid.
- Text before first heading is valid but can be semantically unclear.
- Unknown `as:` values are treated as plain headings with a warning.
