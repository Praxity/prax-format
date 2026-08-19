# Inline Formatting

## Bold and italic

```prax
Use **bold** for emphasis and *italic* for nuance.
```

## Links

```prax
Read the [Safety Manual](https://example.com/safety-manual).
```

Standalone links (a link on its own line) can be transformed into other block types:

```prax
[Download the SOP](https://example.com/sop.pdf)
as: button
variant: outline
openInNewTab: true
```

```prax
[View dashboard](https://app.example.com/widget)
as: embed
height: 420
```

## Inline images

Markdown image syntax is supported.

```prax
![Fire extinguisher location map](assets/extinguisher-map.png)
```

For advanced image params (alt text, captions, sizing), use a bare media path on its own line followed by parameter lines. See [blocks-content.md — image](blocks-content.md#image) for the full parameter reference.

```prax
/assets/extinguisher-map.png
alt: Building floor plan with emergency exits highlighted
caption: Emergency exits marked in green
size: large
```

## Variables

Variables can be referenced inline with moustache syntax:

```prax
Hello {{learnerName}}, welcome back.
```

Variable declarations are block-level lines (not inline). Once declared, a variable persists across the entire course and can be referenced on any subsequent page:

```prax
var: learnerName = "Taylor"
var: attempts = 0
```

Use `camelCase` for variable names. See [document-structure.md](document-structure.md) for extended variable declarations with `type:`, `source:`, and `default:` parameters.

## Lists

Unordered:

```prax
- Item one
- Item two
```

Ordered:

```prax
1. First
2. Second
```

Task lists become checklist blocks only with `as: checklist`.

## Tables

Pipe tables use standard markdown syntax. The separator row (`| --- | --- |`) is optional — the parser skips it if present but does not require it.

```prax
| Risk | Likelihood |
| Slip | Medium |
| Fire | Low |
```

Add `chart:` below a table to render it as a chart. See [blocks-content.md — data-table](blocks-content.md#data-table) for chart parameters.

## Tooltips and the glossary

Brackets contain the visible term, braces contain its definition:

```prax
Workers must wear [PPE]{Personal Protective Equipment — clothing and gear designed to protect the wearer from injury or infection.} at all times.
```

"PPE" renders as a dotted-underlined trigger announced as having a definition. Selecting it
(pointer, Enter, or Space) opens the programmatically associated definition; Escape closes it
and returns focus to the term. It is a toggletip, not a hover-only tooltip, so it works on touch
and by keyboard.

Every defined term is also collected into a **glossary page**, published alongside the course pages and linked from the course menu. Terms are sorted A–Z and deduplicated case-insensitively, so defining `PPE` once and writing `ppe` later produces one entry — the first definition wins. Define a term where a learner first meets it.

Set `design.glossaryPage: false` in the frontmatter to keep the inline tooltips but not publish the aggregated page.

Definitions are plain inline text. Do not nest another tooltip, a link, or a block inside one — a definition that needs that much is a paragraph, not a tooltip.

## Doodles

Inline doodle annotations add hand-drawn decorative marks. Underline doodles use stronger text
weight as well as the hand-drawn stroke so they remain visibly decorative rather than looking
like glossary triggers or links:

```prax
@underline.scribble{Important}
@circle{key concept}
@highlight{remember this}
@highlight{Review the [HousingTO plan]{Toronto's ten-year housing action plan}.}
```

A doodle can wrap a phrase containing a glossary term or link; the annotation remains continuous while the nested term keeps its own interaction.

Types: `@circle`, `@underline`, `@highlight`, `@arrow`, `@box`. Modifiers: `.wavy`, `.thick`, `.thin`, `.dashed`, `.scribble`.

Use sparingly; prefer plain markdown emphasis for compatibility.

## Escaping

Escape reserved syntax with backslash when you need literal text.

```prax
\---
\as: not parsed
\close: not parsed
```

## Inline quality guidelines

- Keep link text descriptive.
- Avoid stacking too many inline styles in one sentence.
