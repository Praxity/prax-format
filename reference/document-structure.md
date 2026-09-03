# Document Structure

## Frontmatter

Frontmatter is optional YAML at the top of the file.

```prax
---
title: Workplace Safety 101
lang: en
kicker: Module 1
design:
  palette: standard
  colorMode: light
---
```

Rules:

- Must start at the first line.
- Uses `---` opening and closing fences.
- Body content starts after closing fence.

`title` is the lesson or module name. `kicker` controls the short label shown above that
title in learner navigation:

```yaml
kicker: Module 1
```

When `kicker` is omitted or blank, no kicker is shown. In a multi-file course, `course.yaml`
supplies the overall course title while each lesson's `title` and optional `kicker` supply its
navigation identity.

## H1 heading — module/lesson title

Praxity Studio's parser recognizes `#` (H1) separately from H2–H4. An H1 produces a `heading` block with `level: 1` and also closes all open implicit groups. Use it for module or lesson titles at the top of a file or section.

```prax
# Module 1: SCORM Round-Trip Coverage
```

H1 is treated as a structural boundary — it does not start a container and takes no `as:` transform.

## Pages and navigation labels

Pages are separated with `---` on its own line. Text after the dashes labels the new page in navigation; it is not rendered as a heading. Use an authored `#` heading when the page needs a visible title.

```prax
## Page One
Content on page one.

--- Introduction

## Page Two
Content on page two.
```

Parser behavior:

- `---` creates a `pageBreak` block.
- `--- Label` creates a `pageBreak` block with `title: "Label"`.
- An unlabelled visible page uses the plain text of its first heading as its navigation label; it appears as `Untitled` only when it has no heading.
- `hide: true` immediately after a page break hides that whole page.
- A page break also closes all open containers and implicit groups.

```prax
--- Internal navigation label
hide: true

# Visible page title
```

Page 1 has no preceding page break, so its explicit navigation label and page defaults live in
frontmatter. Without an explicit label, its first heading provides the same fallback.

```yaml
---
title: Example lesson
firstPage:
  title: Introduction
---
```

Studio narration is a project layer stored in `narration.yaml`, not grammar syntax. Studio owns
that machine-managed sidecar and links its scripts and audio to the visible page title and logical
blocks. Authors and agents should edit the `.prax` source for visible content, then use Studio to
edit narration. The CLI reads the same sidecar and bundles referenced MP3 and optional WebVTT
assets. Narration never autoplays or affects completion, scoring, content access, or navigation.

An optional pronunciation lexicon maps exact written terms to spoken aliases during online
narration generation. It does not change learner-visible text:

```yaml
pronunciation:
  lexemes:
    - grapheme: WCAG
      alias: W C A G
      lang: en
```

Matches are literal, case-sensitive, language-specific, and longest-first. Use separate entries
for capitalization or inflection variants. Course manifests may use the same
`pronunciation.lexemes` shape; lesson-frontmatter entries override matching course entries.

## Headings

Heading levels drive hierarchy and structure.

- `#` page or module content title (H1) — structural boundary
- `##` page-level title
- `###` section/item heading
- `####` subsection heading

Published HTML preserves these authored levels exactly: `#` → `h1`, `##` → `h2`,
`###` → `h3`, and `####` → `h4`. Course and lesson labels stay in navigation and
do not shift content headings down. No heading is synthesized when a page has no authored H1.

```prax
# Module Title
## Incident Response Basics
### Immediate Actions
#### Notify Stakeholders
```

## `as:` transformation

`as:` converts standard markdown structure into a specific block type.

```prax
### Checkpoint Question
as: choice

(x) Correct
( ) Incorrect
```

Common uses:

- `as: choice`, `as: match`, `as: order`, `as: free-response`, `as: rating`
- `as: accordion`, `as: tab`, `as: sequence`, `as: comparison`
- `as: stats`, `as: signature`, `as: checklist`

## Section divider vs page break

- `--` is a divider within a page.
- `---` is a page break between pages.

```prax
## Same Page
Intro text.

--

Additional section on same page.

---

## New Page
```

## Lessons and grouping

You can organize large files using `##` page headings and container sections (`###` items inside accordion/tabs/sequence). No extra lesson keyword is required.

## Universal parameters

All manifest blocks support the following parameters:

| Parameter | Type | Valid values | Description |
|---|---|---|---|
| `layout` | enum | `wide \| full \| breakout` | Overrides the default content width for this block |
| `name` | string | any | Assigns a name to the block for cross-referencing. Used in logic rules (`then: show @myBlock`), assessment-group scoring, and anchor links. Use `camelCase` with no spaces — e.g. `name: safetyTip`. Avoid colons, quotes, and special characters. |
| `hide` | boolean | `true \| false` | Hides the block from rendered output. The block is preserved in the grammar and can be shown later via logic rules (`then: show @name`). |
| `reveal` | `each` | none | On a bulleted or numbered list, shows one item at a time with an accessible learner control. Numeric and timestamp values are retired, leave content visible, and produce an audit warning. |
| `visible` | condition expression | always | Conditional visibility based on variable state. Example: `visible: score >= 80`. The block renders only when the condition is true. |
| `entrance` | enum | `fade \| slide \| scale \| none` | Block entrance animation; overrides course-level `motionEntrance`. |
| `entranceDuration` | CSS duration | `250ms` | Duration of the entrance animation. |

`layout`, `reveal`, and the narration fields are universal parameters in the manifest. The others (`name`, `hide`, `visible`, `entrance`, `entranceDuration`) are runtime workflow metadata recognized by the published-output viewer.

## Escaping reserved lines

If text needs to start with a reserved key (`as:`, `close:`, `if:`, `when:`), escape it with `\`.

```prax
\as: this is literal text, not a block transform
```

## Edge cases

- Malformed YAML frontmatter is ignored as metadata, but body still parses.
- Text before first heading is valid but can be semantically unclear.
- Unknown `as:` values are treated as plain headings with a warning.
