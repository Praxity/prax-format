# Inline icons

Use `@icon{...}` to place a Tabler icon in formatted prose:

```prax
Contact support @icon{mail} or use @icon{IconMail}.
Use @icon{IconMailFilled} for the filled variant.
```

Names may be a kebab-case Tabler name (`mail`, `arrow-right`, or
`mail-filled`) or the corresponding Tabler export name (`IconMail`,
`IconArrowRight`, or `IconMailFilled`). The export name is useful when the
outline and filled exports share a base name. An unknown name remains literal
text so a typo cannot silently become a different icon.

Icons are decorative (`aria-hidden="true"`) and inherit the surrounding text
colour at `1em`. Give an icon-only idea a visible text label; do not rely on an
icon as the only cue for meaning. Icon syntax inside backtick code spans stays
literal.

Icons also work in the formatted display fields listed in
[Inline formatting](reference/inline-formatting.md#where-formatting-works), including
captions, table cells and container labels. An icon can sit inside a Markdown
link label or doodle. The surrounding text still supplies the meaning.

Published output embeds only the validated path data for icons used by the
course. It is rendered as local static SVG after HTML sanitization and does
not load Tabler or an icon CDN at learner runtime.
