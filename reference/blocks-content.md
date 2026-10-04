# Content Blocks

## text

Standard paragraphs (default when no block transform applies).

**Syntax:**
```prax
This is plain paragraph text.
```

**Parameters:**
- `width`: `narrow | wide | full | breakout`

## heading

Headings define content hierarchy. `#` is an ordinary H1 with the same parameters and Studio narration handling as the other levels; it does not split lessons or pages. The number of `#` marks sets the heading level.
The published tag uses the same level without an export-time offset (`#` → `h1`,
`##` → `h2`, `###` → `h3`, `####` → `h4`, `#####` → `h5`, `######` → `h6`).

Use `display: standard|chapter` for the visual heading treatment and `kicker:`
for supporting text above a chapter heading. These do not change its document level.

**Syntax:**
```prax
# Page Heading
## Section Heading
### Subsection Heading
```

**Parameters:**
- `pageStyle` (string), named `pageStyles` entry, on an opening top-level H1.
- `display`: `standard | chapter`, visual heading treatment.
- `kicker` (string), supporting text above a chapter heading.
- `width`: `narrow | wide | full | breakout`

## image

Image from markdown syntax or bare path.

**Syntax:**
```prax
/assets/floorplan.png
alt: Building floor plan
caption: Emergency exits highlighted
size: small
float: left

This paragraph flows beside the image.

## Details

- Headings and lists remain in the same flow.

close: float

This paragraph starts below the image.
```

Write the image source as a bare media path or URL on its own line.

**Parameters:**
- `alt` (string), descriptive text for screen readers. Studio warns when it is missing.
- `decorative` (boolean), set `decorative: true` for purely decorative images that convey no information. Provide either `alt` or `decorative: true`.
- `caption` (string), visible caption below the image.
- `captionEnabled` (boolean), set `false` to suppress the image caption.
- `size` (string), content-relative display size: `small`, `medium`, `large`. Use `width: full` for a full-frame image.
- `alignment` (string), horizontal alignment: `left`, `center`, `right`.
- `ratio`: `square | 4:3 | 3:2 | 16:9`, optional image frame; omit to preserve natural proportions.
- `fit`: `contain | cover`, fit inside a requested frame. Defaults to `contain` when `ratio` is set; `cover` crops to fill it.
- `float` (string): `left | right`. Places the image on the specified physical side. Omit it for the default stacked layout.
- `treatment`: `none | grayscale | tint | duotone | grain | dither | blur`, image treatment.
- `effects` (string), per-image effects that override the course's `design.imageEffects`. List one or more effects separated by spaces, each with an optional intensity in parentheses: `effects: grayscale(40) grain(20)`. `colorWash` takes a colour after its intensity, as in `colorWash(40 #4269d0)`. Use `effects: none` to turn off the course effects for this image. See [image effects](frontmatter-design.md) for each effect's range.
- `width`: `narrow | wide | full | breakout`

Use `fit: contain` for diagrams or documents whose edges carry information. Image framing also works inside cards. Omitting both controls preserves existing image rendering.

Use `size` for image size, `width` for outer block width, and `alignment` for placement. Parameters such as `x`, `filter`, `opacity`, `order`, and standalone `motionBlur` are not canonical public `.prax` syntax. Use the supported image effects instead.

A float region begins at an image with `float: left | right` and continues through
following content, including paragraphs, headings at any level, and lists. `close: float`
ends the region; following content starts below the image. A page break, a new
floated image, or the end of the containing block list also ends it automatically.
Each container has its own scope: a nested `close: float` cannot close an outer float.
An unmatched closer produces a warning. The image and following blocks keep their
individual identities; the closing marker contains no learner content.

The image must use `size: small | medium | large` and must not set `width`.
`alignment` does not enable floating. `wrap` is unsupported and is not an alias.

On narrow screens, the stacked image can use the available width up to its natural
size and stays aligned to its authored float side. It does not keep the smaller
percentage width intended for flowing text beside it.

The image stays stacked until its content container is wide enough to leave about 22
characters for prose. The container thresholds are `20.25em` for `small`, `27em` for
`medium`, and `54em` for `large`, so they follow the learner's text size. Browser print
output is always stacked. In Studio's PDF export, a `small` or `medium` float keeps its
side at 33% or 50% of the text width. Following paragraphs and list items sit beside it
while they fit its height, and the rest continues below. A `large` float stacks, as it
would on a screen as narrow as the printed page.

**Design system image effects** can also be applied at the course level via frontmatter `design.imageEffects`. See [frontmatter-design.md](frontmatter-design.md) for `grayscale`, `colorWash`, `accentLighting`, `progressiveBlur`, `grain`, `halftone`, and other composable effects.

## video

Video from a URL or local media path. The source is the bare URL/path on its own line.

**Syntax:**
```prax
https://www.youtube.com/embed/aqz-KE-bpKQ
caption: Safety walkthrough
title: Emergency response walkthrough
transcript: The presenter checks the exit route before starting work.
start: 12
end: 90
```

For a local video with timed captions, keep the WebVTT file in the project assets:

```prax
/assets/safety-intro.mp4
captions: /assets/safety-intro.en.vtt
caption: Safety walkthrough
```

**Parameters:**
- `caption` (string), visible caption below the video.
- `captions` (WebVTT path or HTTPS URL), synchronized captions for a local/direct video, for example `captions: /assets/intro.fr.vtt`. Studio packages local files with the export; HTTPS caption URLs remain remote and need a network connection.
- `title` (string), visible title above the player.
- `transcript` (string), text shown under the player. Published output uses a heading-wrapped disclosure button with expanded state and a following panel. The transcript stays visible if scripts fail, collapses only after its control works, and prints in full. A file path is displayed literally; Studio does not load that file.
- `start` / `end` (number), optional playback bounds in seconds.
- `captionEnabled` (boolean), set `false` to suppress the visible caption.
- `width`: `narrow | wide | full | breakout`

`start` and `end` are playback bounds in seconds. They are supported for YouTube, Vimeo, local/direct video files, and Mux-hosted video. Loom and unknown iframe embeds can render, but they do not expose reliable playback control to Praxity Studio, so timing bounds are not enforced there.

`autoplay: true` starts local video, YouTube and Vimeo playback when the slide loads. Avoid it for video with sound: audio that plays automatically interrupts screen readers, and browsers often block it. `size` and bare `subtitles:` are not canonical public `.prax` parameters. `caption:` is visible text below the player; `transcript:` is untimed text. Neither creates synchronized captions. For embedded video, supply captions through its video host.

## audio

Audio from a local media path. The source is the bare path on its own line. A warning is issued if no transcript is provided (accessibility).
Published output keeps the native accessible audio controls inside a themed player card. When
no transcript is supplied, the card also includes the audio download link.

**Syntax:**
```prax
/assets/briefing.mp3
title: Daily briefing
caption: Recorded during the morning huddle.
transcript: The supervisor reviews the daily safety checks.
```

**Parameters:**
- `title` (string), display title for the audio player.
- `caption` (string), visible caption below the player.
- `transcript` (string), text shown under the player. Published output uses a heading-wrapped disclosure button with expanded state and a following panel. The transcript stays visible if scripts fail, collapses only after its control works, and prints in full. A file path is displayed literally; Studio does not load that file.
- `captionEnabled` (boolean), set `false` to suppress the visible caption.
- `width`: `narrow | wide | full | breakout`

## divider

Visual section divider within a page, rendered as a line using the course accent color.

**Syntax:**
```prax
--
```

No block-specific parameters. Frontmatter `design.dividerStyle` describes automatic
section transitions; it does not style an explicit `--` divider.

## embed

External embedded content.

**Syntax:**
```prax
https://app.example.com/widget
as: embed
height: 420
```

Write the embed URL on its own line.

**Parameters:**
- `height` (number), iframe height in pixels.
- The embedded frame fills its block. Use universal `width` to set the block width; there is no separate inner-width parameter.
- `width`: `narrow | wide | full | breakout`

## bookmark

External link rendered as a rich preview/bookmark card.

**Syntax:**
```prax
[Praxity Studio](https://praxity.com)
as: bookmark
```

`as: bookmark` uses the same renderer family as embeds, but stores `display: bookmark` internally.
The card shows the linked domain and, when detectable, the resource type (for example,
`amnesty.org.au · PDF`). The full URL remains in the title and accessible link text rather
than being printed as a second line.

**Parameters:**
- `width`: `narrow | wide | full | breakout`

## note

Callout note. Write content as a blockquote (`>`), then add `as: note` to transform it into a styled callout.

**Syntax:**
```prax
> Wear PPE in this zone.
as: note
title: Safety reminder
icon: alert-triangle
color: warning
style: shaded
```

**Parameters:**
- `title` (string), visible callout label; overrides the label derived from `color`.
- `icon` (string), any kebab-case [Tabler Icons](https://tabler.io/icons) outline icon name, for example `thinking-high`, `bulb`, or `sparkles`. Omit it to use the icon derived from `color`; use `none` for no icon.
- `emoji` (string), accepted for backward compatibility but ignored; published callouts use the matching project icon.
- `style`: `outline | shaded` (`light` and `filled` remain accepted aliases)
- `color`: `accent | primary | secondary | success | warning | error | grey`
- `width`: `narrow | wide | full | breakout`

Universal parameters (`name`, `hide`, `visible`, `entrance`, `entranceDuration`) apply to notes as to all blocks. See [document-structure.md](document-structure.md) for details.

**Variants:**
- `style`: `outline` has a transparent background and `shaded` has a semantic tinted fill. Both retain the same 1px semantic border. Legacy `light` maps to `outline`, and `filled` maps to `shaded`.
- `color` maps existing values to the visible localized tone label and hue:

  | Stored value | Published tone | Hue token |
  |---|---|---|
  | omitted / `accent` | Note | `--praxity-color-accent` |
  | `primary` | Info | `--praxity-color-secondary`, falling back to accent |
  | `secondary` | Info | `--praxity-color-secondary`, falling back to accent |
  | `warning` | Warning | `--praxity-color-warning` |
  | `error` | Warning | `--praxity-color-warning` |
  | `success` | Success | `--praxity-color-success` |
  | `grey` | Tip | `--praxity-color-tertiary`, falling back to success |

By default every callout renders the same icon-label-body structure, with a localized label and
matching Tabler icon derived from the color. `title` and `icon` replace those defaults independently,
so color remains supplementary rather than the only signal. The icon occupies a fixed gutter and
scales with the viewer's Larger text setting. HTML and SCORM exports embed only the vector data for
icons the document uses; exported courses do not depend on an icon CDN.

Built-in themes use lightly tinted shaded fills, with distinct Info and Success hues in both
colour modes. The tint strength preserves readable body copy, including Plum light accent notes.

## quote

Quotation with optional speaker, work title, and source URL. Omitted style retains upright
body text, a logical start border, and attribution below the quoted words. Explicit surface
styles change the enclosing figure while preserving semantic quotation and citation markup.

**Syntax:**
```prax
> Consistency beats speed.
speaker: Safety Lead
work: Annual safety review
sourceUrl: https://example.com/safety-review
```

**Parameters:**
- `style`: `none | outline | shaded | primary | secondary`, no chrome, transparent full border, neutral surface, or the corresponding brand tint. Omitted style preserves the original start-border treatment.
- `speaker` (string), person or organisation responsible for the words; rendered as semibold text, never `<cite>`.
- `work` (string), title of the source work; rendered in `<cite>`.
- `sourceUrl` (URL), added to the blockquote's `cite` attribute and shown as a visible link around the work title (or as the URL when no work title is supplied).
- `width`: `narrow | wide | full | breakout`

Studio reads legacy `attribution` as `speaker`. Legacy `decorator`, `size`, and `style`
values such as `cinematic`, `quotation-marks`, and `pullquote` map to
the original quote treatment. Serialization preserves the five current surface styles, writes
legacy attribution as `speaker`, and omits obsolete visual parameters. Quote bodies remain intact.

## dialogue

Use a bullet list for a conversation. Put `as: dialogue` after the list to turn each item into a speaker turn. Each item starts with a speaker key and a colon. Keys are case-insensitive identifiers, never display names. Set the display name in a local `speaker:` declaration or the course cast.

**Syntax:**

```prax
- alex: What should I say when the meeting starts?
- sam: Start with the goal, then ask what everyone needs.
as: dialogue
style: bubbles
caption: Preparing for a meeting
speaker: alex; name: Alex; avatar: /assets/alex.png; side: end
speaker: sam; name: Sam; side: start
```

Start each turn with `- key: words`. Indent continuation lines by at least two spaces. A blank line followed by an indented line starts another paragraph in the same turn. See [list continuation](inline-formatting.md#lists). Without `as: dialogue`, the lines remain a plain list.

**Parameters:**

- `style`: `bubbles | script`. Default: `bubbles`.
- `caption` (text): an optional caption for the exchange.
- `speaker` (repeatable string): `key; name: Display name; avatar: /assets/image.png; voice: voice-id; side: start|end`. Set only the fields you need. Escape a semicolon as `\;` and a colon as `\:` inside field values.
- `width`: `narrow | wide | full | breakout`.

Local `speaker:` declarations apply to this block and override only the cast fields they set. Define shared speakers in the [course cast in `course.yaml`](course-manifest.md#course-cast). Use local `avatar: none` to hide an inherited portrait. Avatars must use project asset paths; remote URLs produce a warning. An undeclared speaker produces a warning and displays its key as the name. The display name stays visible for every turn; avatars are decorative. Studio ignores inline narration fields with a warning. Put narration in `narration.yaml`.

Dialogue turns support inline formatting and multiple paragraphs. They cannot contain nested lists or child blocks. A turn without a speaker key produces a parser warning and a validation error. Studio keeps its text so the author can correct it. Duplicate local speaker declarations produce a warning; the last declaration wins.

`side: start|end` fixes a speaker's bubble to a logical side. A local value overrides the course cast. Without it, the first speaker uses start and all other speakers use end. Invalid sides warn and are ignored. On narrow screens, avatars sit beside names above full-width bubbles. Glossary definitions remain available in the visible tooltip; derived narration speaks only the term in turns and captions.

Each turn is narrated as its own clip in the speaker's voice.

To branch after a dialogue, use a scored choice with `name: reply`, then rules on `block:reply.result` that show named, hidden dialogues. See the [complete branching example](../examples/patterns/dialogue-branching.prax). The standard page computes its fixed authored-order narration playlist at export, including logic-controlled hidden content. A `hidden` logic-controlled wrapper, an ancestor wrapper, or a `.praxity-section` hides its audio; a block without its own wrapper stays eligible unless an ancestor or section is hidden. Inactive tabs, collapsed accordions and other card faces stay eligible and are revealed on selection. Hidden words never play.

The standard page checks authored stops at assessment and media boundaries, even without audio. An active stop applies to the nearest eligible predecessor. A stop is active only when its originating block, every logic-controlled block or section ancestor, and any conditional owner are shown. A terminal stop stays pending if no branch is eligible, then Play enters the branch revealed after submission. Play moves strictly forward after a retry; seek or select to hear an earlier branch. Public `praxity:assessment-response` and `praxity:assessment-group-complete` events pause narration after submission. A private assessment emits no response event, and its option selection and submit use indistinguishable `praxity:block-interaction` events, so private submission alone does not guarantee a pause. Deck media keeps `pauseAfter`; authored stops and branching apply only to the standard page.

If child words of a card, tab or accordion item become conditional, its source anchor changes. The old whole-item recording becomes unlinked, and new item segments have missing audio until the author regenerates them. They are not automatically marked stale.

## code

Fenced code block.

Published output and compiled previews preserve authored indentation and empty
lines. Line numbers and highlights do not add blank rows. Copy reconstructs the
source from those rows, without line numbers or syntax highlighting markup.

**Syntax:**

````prax
```ts
console.log("safe start");
```
````

**Parameters:**
- `language` (optional)
- `width`

## equation

Math block using [KaTeX](https://katex.org/) syntax, fenced with `$$`. Use standard LaTeX math notation. See the [KaTeX supported functions](https://katex.org/docs/supported) for the full reference.

**Syntax:**
```prax
$$
E = mc^2
$$
```

**Parameters:**
- `width`: `narrow | wide | full | breakout`

Equations render once as native MathML in published output and compiled previews.
Inline equations use the same rendering path. Expressions supported by the speech
helper retain a readable speech label on a math wrapper; other expressions expose
native MathML. Fractions and matrices retain their MathML structure.
Invalid expressions remain visible as escaped authored source. No KaTeX HTML
stylesheet is required. This changes rendering only, with no syntax change.

## button

Link rendered as button. Link buttons also work in module decks. Buttons that run
branching actions are not supported in deck mode.

**Syntax:**
```prax
[Open SOP](https://example.com/sop)
as: button
style: outline
openInNewTab: true
```

**Parameters:**
- `text` (required), link label from markdown link text.
- `href` (required), link target from markdown link URL.
- `style`: `filled | outline | light`
- `openInNewTab` (optional)
- `width`

**Variants:**
- `style`: `filled | outline | light`

## data-table

Studio renders a pipe table as a data table or chart. It uses the first row as the header. The parser skips the optional separator row (`| --- | --- |`).

**Syntax:**
```prax
| Quarter | Incidents |
| Q1 | 3 |
| Q2 | 1 |
```

Add `chart:` below a table to render it as a chart:

```prax
| Quarter | Incidents |
| Q1 | 3 |
| Q2 | 1 |
chart: bar
xLabel: Quarter
yLabel: Count
```

**Parameters:**
- `title` (string), table or chart title.
- `caption` (string), visible caption below a table.
- `chart` (string), chart type. When present, renders as a chart instead of a table. Valid values:
  - `bar`, vertical bar chart (default orientation)
  - `line`, line chart
  - `scatter`, scatter plot (renders as line with individual points, no connecting lines)
  - `area`, area chart (line with filled area below)
  - `radar`, radar / spider chart
  - `stacked`, stacked bar chart
  - Note: Studio supports neither `pie` nor `donut`.
- `orientation` (`vertical | horizontal`), bar chart axis orientation. Only applies to `chart: bar`. Default is `vertical`; use `horizontal` for horizontal bars.
- `subtitle` (string), chart subtitle displayed below the title.
- `altText` (string), accessible description of the chart for screen readers.
- `xLabel` (string), horizontal axis label.
- `yLabel` (string), vertical axis label.
- `stacked` (boolean), stack multiple data series.
- `showLines` (boolean), show connecting lines between points. Default `true` for most chart types; default `false` for `scatter`.
- `showPoints` (boolean), show individual data point markers. Default `true` for `scatter`.
- `sortOrder` (`none | asc | desc`), sort data before rendering.
- `width`: `narrow | wide | full | breakout`

Dense annual category axes in vertical bar, line, area, scatter and stacked charts show labels at roughly five-year intervals. Longer series may use wider intervals to keep labels readable. The first and last years always appear; nearby interior labels may be omitted to leave room for them. This applies to consecutive four-digit years in ascending or descending order. Narrow charts may also show fewer labels for other categories. Every data point and accessible data-table row is retained.

Charts in full preview and published output use static SVG, with a centred title, an accessible description, and an equivalent data table for screen readers. Hover and keyboard value inspection are deferred.


**Table examples:**

```prax
| Hazard | Control |
| Chemical splash | Face shield |
| Noise | Hearing protection |
caption: Minimum controls by hazard
```

```prax
| Task | Low risk | High risk |
| Lifting | Team lift | Mechanical assist |
| Cutting | Guard installed | Stop work |
```

`visual: table | chart` is accepted for compatibility but does not change the
published output. Studio labels it unimplemented and offers no value completions.
Use the `chart:` syntax above to author a chart.

The first row supplies column headers by default. An optional Markdown separator row is accepted and skipped; it never becomes a data row.

- `header`: `true | false | row | column`. `true` and `column` mark the first row as column headers; `row` marks the first column as row headers; `false` uses ordinary cells.
- `highlightHeaderRow` and `highlightHeaderCol`: booleans that override `header`. Defaults are `true` and `false`. These controls set header semantics, not just appearance.
- `shadeHeaders`: shade header cells. Default `true`.
- `firstRowLine`: draw a stronger line below the first row. Default `true`.
- `firstColLine`: draw a stronger line after the first column. Default `false`.
- `gridlines`: `none | rows | columns | inside | all`. Default `all`.
- `striped`: alternate row shading. Default `false`.
- `columnAlignments`: comma-separated `left`, `center`, or `right`, one per column. Missing or invalid entries use `left`.

Column widths adjust automatically to the available space and rendered content. When space allows, compact columns fit their contents while longer columns share the remaining width. Tables keep native browser sizing when their content already fits, when all columns need substantial width, or when the container is narrow relative to the text size. Resizing and reading preferences update the layout automatically; no column-sizing parameter is required.

Ordinary words stay intact in table cells. Wide tables and long unbreakable tokens remain available in the keyboard-accessible horizontal scroll region. Use card rows for rich chunks that should stack independently on narrow screens.

Chart `colors` is retained for compatibility but has no effect in canonical output.

## stats

A pipe table transformed into a group of large statistics. The first cell in each row is the value. Studio joins any remaining cells into the label.

```prax
| Over 1 in 4 | Indigenous households have experienced homelessness |
| 3x | the rate experienced by the total population |
| 35% | people counted as homeless who identified as Indigenous |
| 5% | of the national population identified as Indigenous |
as: stats
columns: 2
size: large
caption: Source: [Source title](https://example.com)
```

The statistic value uses the heading font, a heavy weight, and the theme's contrast-safe accent text color. `size` changes the value only. This scale is separate from heading levels, so a value can render larger than an `h1`. The label keeps the body font and regular text color. Stats have no block-level color parameter.

One row renders as a statement. Two or more rows render as a grid. Studio stacks the items when the content area is narrow.

**Parameters:**
- `size`: `default | large | very-large`. The default is `default`.
- `columns`: `2 | 3 | 4`. If omitted, Studio uses the item count up to four columns.
- `caption` (string). Visible source text below the group. Inline markdown links are supported.
- `width`: `narrow | wide | full | breakout`
