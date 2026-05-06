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
description: Comprehensive workplace safety program
locale: en
theme: brand-theme

lessons:
  - intro.prax
  - hazards.prax
  - emergency.prax

design:
  palette: ocean
  accentHue: 200
  density: comfortable
  navArchetype: sidebar

badge: true
```

### Fields

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `title` | string | yes | `""` | Course title |
| `description` | string | no | — | Course description |
| `locale` | string | no | inherited | Default locale (`en`, `fr`, etc.) |
| `theme` | string | no | — | Theme name from `shared/themes/` or built-in |
| `lessons` | string[] | yes | `[]` | Ordered list of `.prax` lesson filenames |
| `design` | object | no | `{}` | Design overrides (same keys as frontmatter `design:`) |
| `badge` | boolean | no | `true` | Show "Made with Praxity" badge on free exports |

### `lessons` list

The `lessons` array is the canonical lesson order. It determines:
- Navigation order in the published course
- File tree display order in the editor
- Export assembly order

Each entry is a filename (not a path) — all lesson files live in the same folder as `course.yaml`.

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
---

Welcome to the safety training program.

---

## Why Safety Matters

Content for page 2...
```

The `title:` in lesson frontmatter is the canonical lesson title. If absent, the filename (minus `.prax`) is the fallback.

`# H1` headings are content headings in multi-file courses — they do not define lesson boundaries (each file is already one lesson).

## Standalone course (no workspace)

A course folder works without a parent `workspace.praxity.yaml`. The user opens the course folder directly. Workspace-level defaults (`defaultTheme`, `defaultLocale`) are unavailable — set them in `course.yaml` instead.

## Single-file courses

A single `.prax` file without a `course.yaml` is still valid. Lessons are defined by `# H1` headings, pages by `---` breaks. No manifest needed. This is the simpler model for quick courses.
