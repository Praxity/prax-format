# Content Blocks

## text

Standard paragraphs (default when no block transform applies).

**Syntax:**
```prax
This is plain paragraph text.
```

**Parameters:**
- `layout`: `wide | full | breakout`

## heading

Headings define structure. The heading level is determined by the number of `#` marks — it is not set via a parameter.

**Syntax:**
```prax
## Page Heading
### Section Heading
#### Subsection Heading
```

**Parameters:**
- `layout`: `wide | full | breakout`

## image

Image from markdown syntax or bare path.

**Syntax:**
```prax
/assets/floorplan.png
alt: Building floor plan
caption: Emergency exits highlighted
```

The image source is the bare media path or URL on its own line — it is not written as a `src:` parameter.

**Parameters:**
- `alt` (string) — descriptive text for screen readers. A warning is issued if missing.
- `decorative` (boolean) — set `decorative: true` for purely decorative images that convey no information. Either `alt` or `decorative: true` should be provided.
- `caption` (string) — visible caption below the image.
- `treatment` (string) — image treatment effect.
- `filter` (string) — CSS-style filter applied to the image.
- `opacity` (number) — opacity value.
- `layout`: `wide | full | breakout`

**Variants:**
- `width`: `small | medium | large`
- `alignment`: `left | center | right`

**Design system image effects** can also be applied at the course level via frontmatter `design.imageEffects`. See [frontmatter-design.md](frontmatter-design.md) for `grayscale`, `colorWash`, `accentLighting`, `progressiveBlur`, `grain`, `halftone`, and other composable effects.

## video

Video from a URL or local media path. The source is the bare URL/path on its own line.

**Syntax:**
```prax
https://www.youtube.com/embed/aqz-KE-bpKQ
caption: Safety walkthrough
subtitles: /assets/captions-en.vtt
transcript: /assets/transcript-en.txt
```

**Parameters:**
- `caption` (string) — visible caption below the video.
- `subtitles` (string) — path to a subtitle/caption file (e.g. WebVTT `.vtt`).
- `transcript` (string) — path to a transcript file in the course `assets/` folder.
- `autoplay` (boolean) — auto-play the video on load.
- `size` (string) — display size.
- `layout`: `wide | full | breakout`

Course-level image effects (from `design.imageEffects`) can also apply to video thumbnails.

## audio

Audio from a local media path. The source is the bare path on its own line. A warning is issued if no transcript is provided (accessibility).

**Syntax:**
```prax
/assets/briefing.mp3
title: Daily briefing
transcript: /assets/briefing-transcript.txt
```

**Parameters:**
- `title` (string) — display title for the audio player.
- `transcript` (string) — path to a transcript file in the course `assets/` folder. Recommended for accessibility.
- `layout`: `wide | full | breakout`

## divider

Visual section divider within a page. Styled according to the course's `design.dividerStyle` setting.

**Syntax:**
```prax
--
```

No parameters. The divider style is controlled at the course level via frontmatter `design.dividerStyle` (`none | thin | gradient | wave | angle | curve`).

## embed

External embedded content.

**Syntax:**
```prax
https://app.example.com/widget
as: embed
height: 420
```

The embed source is the URL on its own line — it is not written as a `src:` parameter.

**Parameters:**
- `height` (number) — iframe height in pixels.
- `layout`: `wide | full | breakout`

## note

Callout note. Write content as a blockquote (`>`), then add `as: note` to transform it into a styled callout.

**Syntax:**
```prax
> Wear PPE in this zone.
as: note
color: warning
style: filled
```

**Parameters:**
- `layout`

**Variants:**
- `style`: `outline | filled`
- `color`: `accent | primary | success | warning | error | grey`

## quote

Quotation with optional attribution.

**Syntax:**
```prax
> Consistency beats speed.
attribution: Safety Lead
decorator: line-left
```

**Parameters:**
- `layout`

**Variants:**
- `decorator`: `line-left | quotation-marks | cinematic`
- `size`: `default | large | very-large`

## code

Fenced code block.

**Syntax:**

````prax
```ts
console.log("safe start");
```
````

**Parameters:**
- `language` (optional)
- `layout`

## equation

Math block using [KaTeX](https://katex.org/) syntax, fenced with `$$`. Supports standard LaTeX math notation — see the [KaTeX supported functions](https://katex.org/docs/supported) for the full reference.

**Syntax:**
```prax
$$
E = mc^2
$$
```

**Parameters:**
- `layout`: `wide | full | breakout`

## button

Link rendered as button.

**Syntax:**
```prax
[Open SOP](https://example.com/sop)
as: button
variant: outline
openInNewTab: true
```

**Parameters:**
- `text` (required)
- `href` (required)
- `openInNewTab` (optional)
- `layout`

**Variants:**
- `variant`: `filled | outline | light`

## data-table

Pipe table rendered as a data table or chart. The first row is treated as the header. The separator row (`| --- | --- |`) is optional — the parser skips it if present but does not require it.

**Syntax:**
```prax
| Quarter | Incidents |
| Q1 | 3 |
| Q2 | 1 |
```

Optional chart rendering — add `chart:` below the table to render it as a visualization:

```prax
| Quarter | Incidents |
| Q1 | 3 |
| Q2 | 1 |
chart: bar
xLabel: Quarter
yLabel: Count
```

**Parameters:**
- `chart` (string) — chart type (`bar`, `line`, `pie`, etc.). When present, renders as a chart instead of a table.
- `xLabel` (string) — horizontal axis label (chart mode).
- `yLabel` (string) — vertical axis label (chart mode).
- `layout`: `wide | full | breakout`

**Variants:**
- `visual`: `table | chart | stats`
