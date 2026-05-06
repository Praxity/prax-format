# .prax Format

Plain-text eLearning courses: human-readable, LLM-friendly, and version-control compatible.

## What is .prax

`.prax` is Praxity's grammar-based course format. A file contains optional YAML frontmatter plus structured body content that can be exported to SCORM, xAPI, or standalone HTML.

## Quick example

```prax
---
title: Workspace Safety Starter
lang: en
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

- 30+ block types across content, flow, assessment, and interactive categories.
- Grammar-first authoring that stays readable as plain text.
- Accessible output patterns built into block semantics.
- Export targets: SCORM 1.2, SCORM 2004, xAPI, standalone HTML.
- Git-friendly diffs and collaboration workflows.
- **LLM-friendly** — paste the skill file as context to generate valid courses with any major model.

## Reference

- [`reference/document-structure.md`](reference/document-structure.md): Frontmatter, pages, headings, `as:`, breaks.
- [`reference/inline-formatting.md`](reference/inline-formatting.md): Bold/italic/links/variables/lists/tables.
- [`reference/blocks-content.md`](reference/blocks-content.md): Content block syntax and options.
- [`reference/blocks-containers.md`](reference/blocks-containers.md): Containers and close rules.
- [`reference/blocks-assessments.md`](reference/blocks-assessments.md): Assessment syntax and scoring params.
- [`reference/blocks-interactive.md`](reference/blocks-interactive.md): Interactive block patterns.
- [`reference/frontmatter-design.md`](reference/frontmatter-design.md): Frontmatter design keys.
- [`reference/course-manifest.md`](reference/course-manifest.md): Multi-file courses and `course.yaml` schema.

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
- [`patterns/scenario-feedback.prax`](examples/patterns/scenario-feedback.prax): Scenario with conditional feedback via variables.
- [`patterns/guided-reading.prax`](examples/patterns/guided-reading.prax): Read-then-test across multiple pages.
- [`patterns/image-comparison.prax`](examples/patterns/image-comparison.prax): Before/after slider comparison.

## Using with LLMs

- Claude Code: point the model to `skill/` and load `skill/SKILL.md` first.
- ChatGPT: paste `skill/SKILL.md`, then add relevant sub-skills.
- Other LLMs: use `skill/SKILL.md` as base context and load sub-skill docs per task.

## Contributing

This specification is maintained by [Praxity](https://praxity.io). We are not accepting pull requests at this time. If you have questions, suggestions, or find an error in the spec, please [open an issue](https://github.com/Praxity/prax-format/issues) or email [hello@praxity.io](mailto:hello@praxity.io).

## License

Specification docs are CC BY 4.0, examples are CC0 1.0, and skill files/code are MIT. See [`LICENSE`](LICENSE).
