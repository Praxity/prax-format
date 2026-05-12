# Document Structure

## Frontmatter

Frontmatter is optional YAML at the top of the file.

```prax
---
title: Workplace Safety 101
lang: en
design:
  palette: standard
  colorMode: light
---
```

Rules:

- Must start at the first line.
- Uses `---` opening and closing fences.
- Body content starts after closing fence.

## H1 heading — module/lesson title

Praxity's parser recognizes `#` (H1) separately from H2–H4. An H1 produces a `heading` block with `level: 1` and also closes all open implicit groups. Use it for module or lesson titles at the top of a file or section.

```prax
# Module 1: SCORM Round-Trip Coverage
```

H1 is treated as a structural boundary — it does not start a container and takes no `as:` transform.

## Pages and lesson markers

Pages are separated with `---` on its own line. Optionally append a label to create a named lesson/section marker.

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
- A page break also closes all open containers and implicit groups.

## Headings

Heading levels drive hierarchy and structure.

- `#` module/lesson title (H1) — structural boundary
- `##` page-level title
- `###` section/item heading
- `####` subsection heading

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

- `as: choice`, `as: match`, `as: order`, `as: free-response`
- `as: accordion`, `as: tab`, `as: sequence`, `as: comparison`
- `as: signature`, `as: checklist`

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
| `reveal` | number, `each`, `true`, or variable name | none | Progressive reveal step. `reveal: 3` means this block appears on step 3 of a click-to-advance sequence. `reveal: each` staggers list items one per click. `reveal: true` reveals on any step. `reveal: someVar` reveals when the variable is truthy. See the language reference Section 10 for full details. |
| `visible` | condition expression | always | Conditional visibility based on variable state. Example: `visible: score >= 80`. The block renders only when the condition is true. |
| `entrance` | enum | `fade \| slide \| scale \| none` | Block entrance animation; overrides course-level `motionEntrance`. |
| `entranceDuration` | CSS duration | `250ms` | Duration of the entrance animation. |

`layout` is the only universal parameter defined in the manifest (`UNIVERSAL_PARAMETERS`). The others (`name`, `hide`, `reveal`, `visible`, `entrance`, `entranceDuration`) are runtime workflow metadata recognized by the published-output viewer.

## Escaping reserved lines

If text needs to start with a reserved key (`as:`, `close:`, `when:`), escape it with `\`.

```prax
\as: this is literal text, not a block transform
```

## Edge cases

- Malformed YAML frontmatter is ignored as metadata, but body still parses.
- Text before first heading is valid but can be semantically unclear.
- Unknown `as:` values are treated as plain headings with a warning.
