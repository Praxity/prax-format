# Inline Formatting

## Bold and italic

```prax
Use **bold** for emphasis and *italic* for nuance.
```

## Links

```prax
Read the [Safety Manual](https://example.com/safety-manual).
```

Standalone links can be transformed with `as: button` or `as: embed`.

## Inline images

Markdown image syntax is supported.

```prax
![Fire extinguisher location map](assets/extinguisher-map.png)
```

For advanced image params, use bare media path + parameter lines.

## Variables

Variables can be referenced inline with moustache syntax:

```prax
Hello {{learnerName}}, welcome back.
```

Variable declarations are block-level:

```prax
var: learnerName = "Taylor"
```

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

Use standard pipe tables.

```prax
| Risk | Likelihood |
| --- | --- |
| Slip | Medium |
| Fire | Low |
```

Add `chart:` below a table to render chart-style data.

## Tooltips and doodles

Inline doodle notation is supported in serializer/parser pipeline:

```prax
@underline.scribble{Important}
```

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
- Use consistent variable names (`camelCase` recommended).
