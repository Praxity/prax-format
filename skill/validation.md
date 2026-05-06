# Validation — .prax Sub-Skill

## Pre-output checklist

Before returning any `.prax` content:

- Confirm frontmatter fences are balanced (`---` ... `---`).
- Confirm every page has at least one `##` heading.
- Confirm page breaks are `---` on their own line.
- Confirm assessment markers match question type.
- Confirm required `close:` statements exist.
- Confirm parameter values use valid types.
- Confirm `as: choice` is used (not `as: choose-one` or `as: choose-many`).
- Confirm `as: rating` is used for the rate block (not `as: rate`).
- Confirm `feedback:` lines are flush left, not indented.
- Confirm `passingScore:` is used in `assessment-group` (not `passing:`).

## Common mistakes

### Wrong assessment `as:` value for choice questions

The parser only accepts `as: choice` for both single-choice and multiple-choice questions. The marker type dispatches the question type.

Wrong:

```prax
### Which PPE is required?
as: choose-one

(x) Hard hat and goggles
( ) Hard hat only
```

Right:

```prax
### Which PPE is required?
as: choice

(x) Hard hat and goggles
( ) Hard hat only
```

Wrong:

```prax
### Select all required items
as: choose-many

[x] Safety glasses
[x] Gloves
```

Right:

```prax
### Select all required items
as: choice

[x] Safety glasses
[x] Gloves
```

### Wrong `as:` value for rating block

The manifest keyword is `rate`, but the parser `as:` value is `rating`. Writing `as: rate` will not parse correctly.

Wrong:

```prax
### How confident are you?
as: rate

1: Not confident
5: Very confident
```

Right:

```prax
### How confident are you?
as: rating

1: Not confident
5: Very confident
```

### Feedback incorrectly indented or placed

Feedback must be on its own line, flush to the left margin, immediately after the option it belongs to. Indenting feedback (with spaces or tabs) causes it to be treated as plain text, not parsed as feedback.

Wrong (indented):

```prax
(x) Correct option
    feedback: This is correct because isolation prevents escalation.
```

Wrong (on same line):

```prax
(x) Correct option feedback: This is correct.
```

Wrong (skipping a line):

```prax
(x) Correct option

feedback: This is correct.
```

Right:

```prax
(x) Correct option
feedback: This is correct because isolation prevents escalation.
```

Feedback for choose-many follows the same rule:

```prax
[x] Safety glasses
feedback: Required in all zones with chemical or flying particle hazards.
[ ] Personal phone
feedback: Phones are not PPE and must be stowed during work.
```

### Wrong assessment markers

- Choose-one must use `(x)` / `( )`.
- Choose-many must use `[x]` / `[ ]`.
- Do not mix marker types in the same question.
- Do not use checkmarks (`✓`), dashes (`-`), or other symbols.

Wrong:

```prax
as: choice
✓ Correct answer
- Wrong answer
```

Right (choose-one):

```prax
as: choice
(x) Correct answer
( ) Wrong answer
```

Right (choose-many):

```prax
as: choice
[x] First correct item
[ ] Incorrect item
[x] Second correct item
```

### Missing `close:` for containers

Some containers require an explicit close statement. Forgetting it will include all following content inside the container.

| Container | Required close |
|---|---|
| columns | `close: col` |
| assessment-group | `close: assessment-group` |
| card (standalone, including `flip: true`) | `close: card` |

Wrong:

```prax
as: col
First column content.

as: col
Second column content.
```

Right:

```prax
as: col
First column content.

as: col
Second column content.

close: col
```

### Wrong `assessment-group` parameter names

The parameter for a group's passing threshold is `passingScore`, not `passing` or `pass-score`.

Wrong:

```prax
## Checkpoint
as: assessment-group
passing: 80
```

Right:

```prax
## Checkpoint
as: assessment-group
passingScore: 80
```

### Accordion `style:` values

The `style:` parameter on accordions controls the visual treatment. Valid values are `default`, `contained`, `separated`.

```prax
### Panel Title
as: accordion
style: contained
```

### `as: branch` is not recognized by the parser

`as: branch` is **not recognized by the parser** — it falls through to an unknown `as:` value and renders as a plain heading. No error is shown, but the block will not behave as a branching scenario.

Do not use `as: branch` in generated `.prax` output. Use conditional visibility (`visible:` with variables) and `when:`/`then:` logic rules for branching behavior instead.

### Forgetting page breaks

Without `---`, everything stays in one page flow. Use page breaks deliberately between modules.

```prax
## Module 1

Content for module 1.

---

## Module 2

Content for module 2.
```

### Wrong fill-blank brace syntax

Fill-blank blanks use single braces: `{word}`. Do not use double braces, angle brackets, or underscores.

Wrong:

```prax
Complete: Workers must wear {{gloves}} and __eyewear__.
```

Right:

```prax
Complete: Workers must wear {gloves} and {eyewear}.
```

### Invalid parameter values

Common type errors:

- String instead of number: `points: "3"` → use `points: 3`
- Misspelled enum: `navArchetype: side-bar` → use `navArchetype: sidebar`
- Boolean typed as text: `required: yes` → use `required: true`
- Wrong feedback indentation: see feedback section above

### Content before first heading

Leading text before the first page heading becomes awkward floating content. Start pages with `##` headings.

## Self-check questions

- Does every page section begin with a clear `##` or `###` heading?
- Are all `as:` values valid for grammar v3?
- Is `as: choice` used for both single- and multiple-choice questions?
- Is `as: rating` used for rating blocks (not `as: rate`)?
- Are choose-one markers `(x)` / `( )` and choose-many markers `[x]` / `[ ]`?
- Is `feedback:` on its own line, flush to the left margin?
- Does every `assessment-group` end with `close: assessment-group`?
- Does every column set end with `close: col`?
- Does every standalone `as: card` (including `flip: true` variants) have a matching `close: card`?
- Is `passingScore` used (not `passing`) in `assessment-group`?
- Are shared assessment params typed correctly (numbers as numbers, booleans as true/false)?
- Is frontmatter valid YAML?
- Are fill-blank blanks using single braces `{word}`?

## Final parser gate

Run parser validation before publishing examples or generated output.
If parser output differs from expected structure, fix syntax first before adding more content.

Parser errors to watch for:

- Unmatched `close:` (no matching open container)
- Assessment options with no heading above them
- Frontmatter with unclosed `---` block
- YAML syntax errors (unquoted colons, bad indentation)
