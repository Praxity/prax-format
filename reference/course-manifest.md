# Course Manifest (`course.yaml`)

A `.prax` course can be a single file or a multi-file project. Multi-file courses use a `course.yaml` manifest to define lesson order, course metadata, and design overrides.

## Folder structure

```
safety-training/              <- course folder
  course.yaml                 <- manifest (required)
  assets/                     <- course-level assets
    hero.webp
  intro.prax                  <- lesson file
  hazards.prax                <- lesson file
  emergency.prax              <- lesson file
```

Each `.prax` file in the folder is a standalone lesson. Pages within a lesson are separated by `---` as usual.

## Schema

```yaml
title: Safety Training Fundamentals
id: 4b827bfd-f5ea-4ba6-a8aa-5fb5113cdef4
description: Comprehensive workplace safety program
locale: en
theme: brand-theme
narrationEnabled: true

lessons:
  - intro.prax
  - hazards.prax
  - emergency.prax

design:
  palette: ocean
  accentHue: 200
  density: comfortable
  navArchetype: sidebar
```

### Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `title` | string | yes | `""` | Course title |
| `id` | string | no | Studio-generated | Stable, non-sensitive course identity used for learner state. Preserve it after Studio adds it. |
| `description` | string | no | — | Course description |
| `locale` | string | no | inherited | Default locale (`en`, `fr`, etc.) |
| `theme` | string | no | — | Theme name from `shared/themes/` or built-in |
| `narrationEnabled` | boolean | no | `false` | Enables block-level narration unless a lesson overrides it |
| `lessons` | string[] | yes | `[]` | Ordered list of `.prax` lesson filenames |
| `design` | object | no | `{}` | Design overrides (same keys as frontmatter `design:`) |

### `lessons` list

The `lessons` array is the canonical lesson order. It determines:

- Navigation order in the published course
- File tree display order in the editor
- Export assembly order

Each entry is a filename (not a path) — all lesson files live in the same folder as `course.yaml`.

Full-course HTML and SCORM exports list every lesson in this order. Each lesson link
uses that file's frontmatter `title` and opens its first page. Only the current lesson
shows its nested page links. There are no lesson disclosure controls, and learners may
visit other lessons freely.

Previous and Next follow one sequence across all lesson pages. At a forward lesson
boundary the action reads `Continue to [lesson title]`; Previous returns to the prior
lesson's final page. Position labels show the current module and its local page count,
for example `Module 3 of 6 · Page 4 of 11`. Bookmarking resumes the exact page.
SCORM exports remain one package with one SCO and one course completion record.

The glossary collects inline terms from every listed lesson. Its link sits outside the
lesson list and opens a new tab, leaving the current course page in place. The glossary
does not start an LMS session or contribute to progress, completion, or Previous/Next.
Electron preview opens the same glossary in a separate protected window.

### Stable course identity

Studio adds `id` to a valid manifest when it first opens the course. The value stays the same
across preview regeneration and publishing so learner state remains attached to the course.
Preserve it when editing the manifest. Do not reuse it for a copied course that should have
independent learner state.

### `design` overrides

The `design` object accepts the same keys as lesson frontmatter `design:`. See [frontmatter-design.md](frontmatter-design.md) for all options. Course-level design applies to every lesson unless a lesson's own frontmatter overrides it.

## Settings inheritance

Settings cascade from workspace to course to lesson:

```
workspace.praxity.yaml  (defaultTheme, defaultLocale)
  | overridden by
course.yaml             (theme, locale, design)
  | overridden by
lesson frontmatter      (design overrides)
```

A lesson's frontmatter `design:` block takes highest precedence. If absent, the course-level `design:` from `course.yaml` applies. If that's also absent, workspace defaults apply.

`course.yaml` may enable narration for the course with `narrationEnabled`. Lesson enablement,
generation defaults, scripts, anchors, and assets live in the Studio-managed `narration.yaml`
sidecar rather than lesson frontmatter.

## Manifest integrity

`course.yaml` is the source of truth. File operations must keep it in sync:

| Operation | Manifest update |
|-----------|-----------------|
| Rename a `.prax` file | Update the filename in `lessons:` |
| Delete a `.prax` file | Remove from `lessons:` |
| Create a new `.prax` file | Append to `lessons:` |
| Drag a `.prax` file into course | Append to `lessons:` |

### Invalid state handling

| Condition | Behavior |
|-----------|----------|
| `lessons:` references a file that doesn't exist | Skip on export, warn in editor |
| `.prax` file on disk but not in `lessons:` | Shown dimmed as "(unlisted)" in editor |
| Duplicate entry in `lessons:` | Deduplicated on load, first occurrence kept |
| `course.yaml` missing | Folder treated as non-course |
| No `lessons:` key | Auto-discover `.prax` files alphabetically |

## Lesson files

Each lesson file is a standalone `.prax` document. Frontmatter is optional:

```
---
title: Introduction to Safety
kicker: Module 1
---

Welcome to the safety training program.

---

## Why Safety Matters

Content for page 2...
```

The `title:` in lesson frontmatter is the canonical lesson title. If absent, the filename (minus `.prax`) is the fallback.

Lesson `kicker:` text is excluded from the multi-file course outline. Include a label such
as `Module 1:` in the lesson `title:` when wanted. Single-lesson exports retain their flat
page outline and existing kicker treatment.

`# H1` headings are content headings in multi-file courses — they do not define lesson boundaries (each file is already one lesson).

## Standalone course (no workspace)

A course folder works without a parent `workspace.praxity.yaml`. The user opens the course folder directly. Workspace-level defaults (`defaultTheme`, `defaultLocale`) are unavailable — set them in `course.yaml` instead.

## Single-file courses

A single `.prax` file without a `course.yaml` is still valid. Pages are separated by `---` breaks. `# H1` headings can be used as structural titles but do not create formal lesson boundaries — the entire file is treated as one course. No manifest needed. This is the simpler model for quick courses.
