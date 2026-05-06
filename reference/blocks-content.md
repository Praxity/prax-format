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

Headings define structure.

**Syntax:**
```prax
## Page Heading
### Section Heading
#### Subsection Heading
```

**Parameters:**
- `level` (number, optional)
- `layout`

## image

Image from markdown syntax or bare path.

**Syntax:**
```prax
/assets/floorplan.png
alt: Building floor plan
caption: Emergency exits highlighted
```

**Parameters:**
- `src` (required)
- `alt` (required unless `decorative: true`)
- `decorative` (boolean)
- `caption` (string)
- `layout`

> **`alt` and `decorative` are mutually exclusive.** Either provide descriptive `alt` text OR set `decorative: true`. Images require one or the other — `alt` is enforced unless the image is explicitly marked decorative.

**Variants:**
- `width`: `small | medium | large`
- `alignment`: `left | center | right`

## video

Video URL or media path.

**Syntax:**
```prax
https://www.youtube.com/embed/aqz-KE-bpKQ
caption: Safety walkthrough
```

**Parameters:**
- `src` (required)
- `caption` (optional)
- `layout`

## audio

Audio media source.

**Syntax:**
```prax
/assets/briefing.mp3
title: Daily briefing
```

**Parameters:**
- `src` (required)
- `title` (optional)
- `layout`

## divider

Section divider inside a page.

**Syntax:**
```prax
--
```

**Parameters:**
- `layout`

## embed

External embedded content.

**Syntax:**
```prax
https://app.example.com/widget
as: embed
height: 420
```

**Parameters:**
- `src` (required)
- `layout`

## note

Callout note based on blockquote + transform.

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
```prax
\`\`\`ts
console.log("safe start");
\`\`\`
```

**Parameters:**
- `language` (optional)
- `layout`

## equation

LaTeX block in `$$` fences.

**Syntax:**
```prax
$$
E = mc^2
$$
```

**Parameters:**
- `layout`

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

Table-like content (table or chart rendering model).

**Syntax:**
```prax
| Quarter | Incidents |
| --- | --- |
| Q1 | 3 |
| Q2 | 1 |
```

Optional chart rendering:

```prax
| Quarter | Incidents |
| --- | --- |
| Q1 | 3 |
| Q2 | 1 |
chart: bar
xLabel: Quarter
yLabel: Count
```

**Parameters:**
- `layout`

**Variants:**
- `visual`: `table | chart | stats`
