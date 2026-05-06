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

## Using with LLMs

- Claude Code: point the model to `skill/` and load `skill/SKILL.md` first.
- ChatGPT: paste `skill/SKILL.md`, then add relevant sub-skills.
- Other LLMs: use `skill/SKILL.md` as base context and load sub-skill docs per task.

## License

Specification docs are CC BY 4.0, examples are CC0 1.0, and skill files/code are MIT. See [`LICENSE`](LICENSE).
