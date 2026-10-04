# .prax Format

Plain-text eLearning courses: human-readable, LLM-friendly, and version-control compatible.

## What is .prax

`.prax` is Praxity Studio's grammar-based course format. A file contains optional YAML frontmatter plus structured body content. Studio exports it to SCORM, xAPI, standalone HTML, or a printable PDF workbook.

## Studio 0.3.0 compatibility

Existing grammar v3 courses need no source migration to open in Studio 0.3.0.
Saving can normalize syntax and escaping. Keep a copy if a course must reopen
in Studio 0.2.0. Both versions use grammar v3; older `:::` block fences remain unsupported.

These constructs require Studio 0.3.0 or later:

| Construct | Reference | Studio 0.2.0 behavior |
| --- | --- | --- |
| Dialogue turns, course cast and speaker sides | [Dialogue](reference/blocks-content.md#dialogue) | Keeps list and parameter text without a dialogue block. |
| Dropdown and word-bank blanks | [Fill blanks](reference/blocks-assessments.md#fill-blank) | Uses text inputs instead of selection controls. |
| Rectangle, ellipse and polygon hotspots | [Hotspots](reference/blocks-assessments.md#hotspot) | Drops the geometric regions. |
| Continue gate with `because "reason"` | [Continue gates](reference/document-structure.md#continue-gates) | Treats the action as a literal show target and does not block completion. |
| H5 and H6 headings | [Headings](reference/document-structure.md#headings) | Keeps the hash-prefixed lines as plain text. |
| Indented or multiple-paragraph list items | [Lists](reference/document-structure.md#lists) | Splits continuation text into separate blocks. |
| Escaped inline punctuation and field delimiters | [Escaping](reference/inline-formatting.md#escaping) | Can retain backslashes or apply formatting to escaped text. |
| Consecutive column rows separated by `close: col` | [Columns](reference/blocks-containers.md#columns) | Merges the rows into one column group. |

The references also cover card heading inference and `close: flashcard`, rating
feedback, named hidden blocks and logic field references, and malformed
frontmatter warnings. These fixes preserve content when saving. New interactions
still need the newer application to render them.

## Studio 0.2.0 syntax

Studio 0.2.0 separates block width from container presentation and image size. This is a breaking canonical syntax change during alpha: saving writes the new names, and those files require Studio 0.2.0 or later. Keep a copy before saving if you need to reopen a course in an earlier Studio version.

| Purpose | Canonical syntax |
| --- | --- |
| Outer width of any block | `width: narrow\|wide\|full\|breakout` |
| Card presentation | `layout: grid\|masonry\|slides\|rows` |
| Image size within its block | `size: small\|medium\|large` |
| Relative share of a column | `weight: 2` with another column's `weight: 1` |
| Comparison arrangement | `layout: side-by-side\|slider` |

Omit `width` for normal content width. Width and presentation are independent: a card can combine `layout: slides` with `width: narrow`. Sequences retain `style: numbered|timeline|none` and their separate `orientation`. Embeds use the universal width; they have no separate inner-width control.

### Reading older files

Studio 0.2.0 reads the older width-valued `layout` parameter, card `layoutMode` and `layout: single`, image `width: small|medium|large`, numeric column `width`, and comparison `style`. Saving converts these to the canonical names above, including settings inside `design.componentDefaults`.

A valid canonical value takes precedence over a conflicting legacy alias regardless of source order, and Studio reports the conflict. Invalid canonical values produce diagnostics; a valid legacy value can still be retained. Numeric or percentage embed widths and image `size: full-width` are not supported aliases.

## Quick example

```prax
---
title: Workspace Safety Starter
lang: en
kicker: Module 1
design:
  palette: standard
  colorMode: light
  accentHue: 220
---

## Welcome

This short course introduces a simple safety routine you can run before starting work.

/assets/safety-checklist.png
alt: Checklist on a clipboard
caption: Daily pre-shift checklist

---

## Before You Begin

Confirm emergency exits are clear and protective gear is available.
```

## Who it's for

- Instructional designers editing course files directly.
- Educational developers building reusable modules.
- LLM users generating draft courses from outlines.
- Tool authors integrating `.prax` into pipelines.

## Key features

- Every authoring keyword in Studio's block manifest.
- Grammar-first authoring that stays readable as plain text.
- Accessible output patterns built into block semantics.
- Export targets: SCORM 1.2, SCORM 2004, xAPI, standalone HTML, and a tagged PDF/UA-1 workbook. The [CLI](cli.md) exports every target except xAPI.
- Git-friendly diffs and collaboration workflows.
- Works with language models: give a model the [authoring skill](skill/SKILL.md) as context and it can draft valid courses.

## Reference

- [`reference/document-structure.md`](reference/document-structure.md): Frontmatter, pages, headings, `as:`, breaks.
- [`reference/inline-formatting.md`](reference/inline-formatting.md): Bold/italic/links/variables/lists/tables.
- [`inline-icons.md`](inline-icons.md): Inline Tabler icons and accessibility guidance.
- [`reference/blocks.md`](reference/blocks.md): Studio block index with links to each detailed reference section.
- [`reference/blocks.json`](reference/blocks.json): Machine-readable block index with parameters and variants.
- [`reference/blocks-content.md`](reference/blocks-content.md): Content block syntax and options.
- [`reference/blocks-containers.md`](reference/blocks-containers.md): Containers and close rules.
- [`reference/blocks-assessments.md`](reference/blocks-assessments.md): Assessment syntax and scoring params.
- [`reference/blocks-interactive.md`](reference/blocks-interactive.md): Interactive block patterns.
- [`reference/frontmatter-design.md`](reference/frontmatter-design.md): Frontmatter design keys.
- [`reference/course-manifest.md`](reference/course-manifest.md): Multi-file courses and `course.yaml` schema.
- [`reference/module-deck.md`](reference/module-deck.md): Narrated modules, slide pauses, assessment gates, and learner controls.
- [`cli.md`](cli.md): Headless Studio export command and JSON result contract.

## Examples

- [`examples/minimal.prax`](examples/minimal.prax): Minimal two-page starter.
- [`examples/quiz-course.prax`](examples/quiz-course.prax): Assessment-focused module.
- [`examples/interactive-module.prax`](examples/interactive-module.prax): Containers plus interactions.
- [`examples/full-featured.prax`](examples/full-featured.prax): Broad feature coverage file.

## Instructional patterns

Reusable templates for common eLearning designs:

- [`patterns/faq-accordion.prax`](examples/patterns/faq-accordion.prax): FAQ page with expandable panels.
- [`patterns/card-gallery.prax`](examples/patterns/card-gallery.prax): Team or concept cards in a grid.
- [`patterns/flashcard-drill.prax`](examples/patterns/flashcard-drill.prax): Flip-card vocabulary drill.
- [`patterns/multicol-signature.prax`](examples/patterns/multicol-signature.prax): Completion page with summary and signoff.
- [`patterns/horizontal-timeline.prax`](examples/patterns/horizontal-timeline.prax): Process steps on a horizontal timeline.
- [`patterns/assessment-choice.prax`](examples/patterns/assessment-choice.prax): Learner picks which question to answer.
- [`patterns/matrix-likert.prax`](examples/patterns/matrix-likert.prax): Likert matrix with scale points and statements.
- [`patterns/scenario-feedback.prax`](examples/patterns/scenario-feedback.prax): Scenario with conditional feedback via variables.
- [`patterns/guided-reading.prax`](examples/patterns/guided-reading.prax): Read-then-test across multiple pages.
- [`patterns/image-comparison.prax`](examples/patterns/image-comparison.prax): Before/after slider comparison.
- [`patterns/card-layouts.prax`](examples/patterns/card-layouts.prax): Masonry cards and label-beside-body rows.
- [`patterns/image-layouts.prax`](examples/patterns/image-layouts.prax): Image sizes, wrapped images and weighted columns.
- [`patterns/data-display.prax`](examples/patterns/data-display.prax): Key figures, a data table, numbered sources and an inline icon.
- [`patterns/assessment-variants.prax`](examples/patterns/assessment-variants.prax): Star and slider ratings and a private, downloadable response.
- [`patterns/module-deck.prax`](examples/patterns/module-deck.prax): Narrated module stops, a knowledge-check gate and deck settings.
- [`patterns/fill-blank-styles.prax`](examples/patterns/fill-blank-styles.prax): Dropdown and word-bank blanks.
- [`patterns/dialogue-branching.prax`](examples/patterns/dialogue-branching.prax): Scored choice revealing one of two dialogue branches on a standard page.
- [`patterns/continue-gate.prax`](examples/patterns/continue-gate.prax): A prerequisite check with an authored Continue reason.

## Card syntax (v3.1)

Use `as: card` for both static card grids and flip-card carousels.

```prax
## Safety Terms
as: card
layout: slides
style: outline
shadow: subtle
advance: 0
transition: fade
showProgress: true
shuffle: false
trackCompletion: true

### PPE
Personal Protective Equipment

card: back
Helmet, eye protection, gloves

close: card
```

The `## Safety Terms` heading is the card group title. Each `###` heading starts a card item and becomes that card's label/header; it is not part of the flippable face content.

For non-flip cards, omit `card: back` and use `layout: grid` (or `masonry`) plus `columns`.

`as: flashcard` is a deprecated backward-compat alias and should not be used for new content.

## Image syntax (v3.1)

Image blocks support `size` and `alignment` variants plus standard content params:

- Variants: `size: small|medium|large`, `alignment: left|center|right`
- Params: `alt`, `decorative`, `caption`, `float: left|right`
- Use `close: float` to end text flowing beside an image. See [image float rules](reference/blocks-content.md#image).
- `treatment` is deprecated; use `effects` instead

For a full-frame image, use the universal `width: full`; `full` is not an image `size` value.

`effects` is composable and accepts either `none` or an effects map. Supported effects:

- `grayscale: { intensity }`
- `colorWash: { intensity, color }`
- `accentLighting: { intensity }`
- `progressiveBlur: { intensity }`
- `accentBlur: { intensity }`
- `motionBlur: { intensity, direction }`
- `grain: { intensity }`
- `halftone: { intensity }`
- `dithering: { intensity }`

Design-level `design.imageEffects` (frontmatter) applies to all images. Per-image `effects:` overrides that default.

Not supported image params:

- `filter`
- `opacity`
- `x` (use `alignment`)
- `order`
- standalone `motionBlur` boolean (use `effects.motionBlur`)

## Using with LLMs

- Claude Code: point the model to `skill/` and load `skill/SKILL.md` first.
- ChatGPT: paste `skill/SKILL.md`, then add relevant sub-skills.
- Other LLMs: use `skill/SKILL.md` as base context and load sub-skill docs per task.

## Contributing

This specification is maintained by [Praxity Studio](https://praxity.io). We are not accepting pull requests at this time. If you have questions, suggestions, or find an error in the spec, please [open an issue](https://github.com/Praxity/prax-format/issues) or email [hello@praxity.io](mailto:hello@praxity.io).

## License

Specification docs are CC BY 4.0, examples are CC0 1.0, and skill files/code are MIT. See [`LICENSE`](LICENSE).
