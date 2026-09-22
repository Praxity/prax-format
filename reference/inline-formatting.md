# Inline Formatting

## Bold and italic

```prax
Use **bold** for emphasis and *italic* for nuance.
```

Use `~~removed text~~` for strikethrough and `==marked text==` for a plain
highlight. Backtick code spans keep their contents literal, including markup.

## Where formatting works

Inline formatting works in prose, headings, note bodies, lists and nested block
content. Display-field preparation also covers image/audio/video captions,
audio/video titles, note titles, heading kickers, table headings and cells,
accordion/tab/card/sequence labels, checklist labels, chart titles and captions,
statistics labels and captions, and assessment group names. Assessment stems,
supporting prose and feedback use their child blocks; choice and order labels
and matching prompts also accept formatting.

URLs, alt text, IDs, code, equation source and grading values remain literal.
Formatting a displayed answer label does not change its stored answer value.
Native answer choices and machine-valued fields are not general rich-text containers.

Links may contain decorative formatting, icons and code. Doodles may contain
other doodles, links and glossary terms. Inside a link label or an interactive
control label, nested links become label text and glossary terms become
`term (definition)`, without another interactive control. Accordion and tab
labels follow this rule. Code remains literal at every nesting level.

## Links

```prax
Read the [Safety Manual](https://example.com/safety-manual).
```

Standalone links (a link on its own line) can be transformed into other block types:

```prax
[Download the SOP](https://example.com/sop.pdf)
as: button
style: outline
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

Use `camelCase` for variable names. See [Variables and logic](../skill/SKILL.md#variables-and-logic) for extended variable declarations with `type:`, `source:`, and `default:` parameters.

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

## Numbered sources

Place a source beside the statement it supports:

```prax
Housing need includes affordability and suitability.@footer{CMHC, Core housing need, 2025.}
```

The output shows `[1]` in superscript. Numbers follow source order and restart at
1 on each page. Sources are collected into an ordered list at the bottom of that
page, before page navigation. They are not added to the course glossary.

To place the list yourself, put `@footer.insert` in its own paragraph. It can sit
inside an accordion, and includes references both before and after that location:

```prax
### Sources
as: accordion

@footer.insert

close: accordion
```

The first standalone insertion wins; later standalone insertions are empty. With
no references, no list is shown. An insertion written within a sentence stays
literal. References inside inline or fenced code stay literal too.

Source text supports bold, italic, inline code and Markdown links. Do not nest
brace-based inline syntax inside a source. Numbering is generated for rendering,
never written into the `.prax` file. Use references in formatted prose, headings,
notes, lists, display captions and container content or labels, not machine
metadata or grading values.

References are non-interactive text with a screen-reader prefix, "Footnote" in
English and "Note" in French. The sources retain native ordered-list semantics.
No link role or keyboard stop is added. W3C's `doc-noteref` role inherits link
semantics, so it is not used for these plain references.
[DPUB-ARIA 1.1](https://www.w3.org/TR/dpub-aria-1.1/#doc-noteref)

## Inline icons

Use a Tabler icon name inside `@icon{...}`:

```prax
@icon{mail} Contact your instructor.
@icon{star-filled} Save this resource.
```

Kebab-case names use the outline icon by default; append `-filled` for a filled
variant. Tabler export names such as `IconMail` and `IconMailFilled` also work.
Icons are decorative and hidden from screen readers, so keep meaningful text
beside them. Code spans keep icon syntax literal.

Page titles and navigation labels are plain text, so do not put `@icon{...}` in
a page-break title. Use icons in content headings instead. In deck outlines,
assessment pages receive a separate assessment icon automatically.

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

Plain `==highlight==` has a visible fill even when `highlightStyle: none` disables
decorative treatment. `@box{text}` surrounds its text; `@arrow{text}` points to
its own text and works in the final block of a page.
