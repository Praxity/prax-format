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

## H1 heading — module/lesson title

Praxity Studio's parser recognizes `#` (H1) separately from H2–H4. An H1 produces a `heading` block with `level: 1` and also closes all open implicit groups. Use it for module or lesson titles at the top of a file or section.

```prax
# Module 1: SCORM Round-Trip Coverage
```

H1 is treated as a structural boundary — it does not start a container and takes no `as:` transform.

## Pages and navigation labels

Pages are separated with `---` on its own line. Text after the dashes labels the new page in navigation; it is not rendered as a heading. Use an authored `#` heading when the page needs a visible title.

```prax
## Page One
Content on page one.

--- Introduction

## Page Two
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
values during migration. Studio's rename and Save As commands preserve generated identities.
Unsaved previews do not replace saved identity records.

Studio preserves identities when it can match content unambiguously. Ambiguous duplicate or
external edits receive new IDs rather than inheriting another block's saved learner state.

Studio narration is a project layer stored in `narration.yaml`, not grammar syntax. Studio owns
that machine-managed sidecar and links its scripts and audio to the visible page title and logical
blocks. Authors and agents should edit the `.prax` source for visible content, then use Studio to
edit narration. The CLI reads the same sidecar and bundles referenced MP3 and optional WebVTT
assets. In the default page runtime, narration never autoplays or affects completion, scoring, content access, or navigation. The experimental [module-deck compiler option](module-deck.md) adds user-started continuous playback and slide navigation. Its knowledge-check gates remain independent of listening time.

Cards, sequences, accordions, and tabs generate separate narration clips for each item,
with a separate clip for any container title or instructions. Two-sided cards have separate
front and back clips, in that order; each face has its own narration settings. Nested containers retain their
own item boundaries. Preview and export play the clips in authored order. This keeps a long
container from consuming Soniox's two-minute limit in one request; an individual item still
needs to fit that limit. Existing whole-container recordings remain playable. Regenerating a
derived recording replaces it with item clips only after the full replacement batch succeeds;
explicit custom whole-container scripts remain intact.

Generated narration for single- and multiple-choice questions reads the prompt, description,
instructions, and visible answer options in authored order, then pauses for the learner.
It excludes correctness markers, scoring, answer keys, and feedback. Custom narration scripts
override this generated text. After a generated script changes, regenerate its audio and timings;
existing clips are not rewritten automatically.

Narration scripts also supply the full reading text in the module transcript. In Studio's script
field, use `**bold**`, `*italic*`, `#` through `###` headings, `-` bullet items, and `1.` numbered
items. Put each heading or list item on its own line, and separate paragraphs with blank lines.
Lists are flat. Transcript headings keep the surrounding text size. Raw HTML and other inline
features are literal text, not executable markup.

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

- `#` page or module content title (H1) — structural boundary
- `##` page-level title
- `###` section/item heading
- `####` subsection heading

Published HTML preserves these authored levels exactly: `#` → `h1`, `##` → `h2`,
`###` → `h3`, and `####` → `h4`. Course and lesson labels stay in navigation and
do not shift content headings down. No heading is synthesized when a page has no authored H1.

```prax
# Module Title
## Incident Response Basics
### Immediate Actions
#### Notify Stakeholders
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

## Lessons and grouping

You can organize large files using `##` page headings and container sections (`###` items inside accordion/tabs/sequence). No extra lesson keyword is required.

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
| `name` | string | any | Assigns a name to the block for cross-referencing. Used in logic rules (`then: show @myBlock`), assessment-group scoring, and anchor links. Use `camelCase` with no spaces — e.g. `name: safetyTip`. Avoid colons, quotes, and special characters. |
| `hide` | boolean | `true \| false` | Hides the block from rendered output. The block is preserved in the grammar and can be shown later via logic rules (`then: show @name`). |
| `visible` | condition expression | always | Conditional visibility based on variable state. Example: `visible: score >= 80`. The block renders only when the condition is true. |
| `entrance` | enum | `fade \| slide \| scale \| none` | Block entrance animation; overrides course-level `motionEntrance`. |
| `entranceDuration` | CSS duration | `250ms` | Duration of the entrance animation. |

`width: narrow` centers a block in a measure capped at `45ch`, using the surrounding body font. It keeps the full available width in a narrower parent or viewport and preserves text alignment. Use it for short passages such as notes and quotes on pages or slides. Omit `width` for the normal content width. Cards can combine a presentation such as `layout: slides` with `width: narrow`.

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

## Edge cases

- Malformed YAML frontmatter is ignored as metadata, but body still parses.
- Text before first heading is valid but can be semantically unclear.
- Unknown `as:` values are treated as plain headings with a warning.
