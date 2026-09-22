# Validation — .prax Sub-Skill

## Pre-output checklist

Before returning any `.prax` content:

- Confirm frontmatter fences are balanced (`---` ... `---`).
- Confirm intended visible page headings are authored explicitly.
- Confirm page breaks use `---`, optionally followed by a navigation label.
- Confirm assessment markers match question type.
- Confirm container boundaries match the intended structure; `--` does not close containers.
- Confirm parameter values use valid types.
- Confirm `as: choice` is used (not `as: choose-one` or `as: choose-many`).
- Confirm `as: rating` is used for the rate block rather than `as: rate`.
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

The manifest keyword is `rate`, and the parser `as:` value is `rating`. Writing `as: rate` will not parse correctly.

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

### Feedback placement

Feedback must be on its own line immediately after the option it belongs to.
Indentation is accepted; prefer flush-left formatting for consistency.

Accepted (indented):

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

Use an explicit close statement before following same-page content that belongs outside the container. A page break or end of file also closes every container.

| Container | Explicit closer |
|---|---|
| columns | `close: col` |
| assessment-group | `close: assessment-group` |
| card, including flip cards using `card: back` | `close: card` |

Following prose is still inside the second column:

```prax
as: col
First column content.

as: col
Second column content.

This should be outside the columns.
```

Right:

```prax
as: col
First column content.

as: col
Second column content.

close: col

This is outside the columns.
```

### Wrong `assessment-group` parameter names

The parameter for a group's passing threshold is `passingScore`, not `passing`, `pass`
or `pass-score`. There is no per-question pass threshold.

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

The `style:` parameter on accordions controls the visual treatment. New authoring uses `none`, `outline`, `shaded`, `primary`, `secondary`. Omitted style preserves separator lines; legacy `default`, `contained` and `separated` remain accepted.

```prax
### Panel Title
as: accordion
style: shaded
```

### Forgetting page breaks

Without `---`, everything stays in one page flow. Use page breaks deliberately between modules.

```prax
## Module 1

Content for module 1.

---

## Module 2

Content for module 2.
```

### Wrong fill-blank marker syntax

Fill-blank blanks use `{word}` for a known answer or `____` for an open answer. Do not use
double braces, angle brackets, or fewer than four underscores.

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

### Visible page headings

Pages may start with any content. Add an authored heading when a visible page title is intended.

## Self-check questions

- Do intended visible page titles have authored headings?
- Are all `as:` values valid for grammar v3?
- Is `as: choice` used for both single- and multiple-choice questions?
- Does each rating block use `as: rating` rather than `as: rate`?
- Are choose-one markers `(x)` / `( )` and choose-many markers `[x]` / `[ ]`?
- Is `feedback:` on its own line, flush to the left margin?
- Does each assessment group end before content intended outside it?
- Does `close: col` precede content intended outside the columns?
- Do card boundaries keep following page content outside the cards?
- Is `passingScore` used (not `passing`) in `assessment-group`?
- Are shared assessment params typed correctly (numbers as numbers, booleans as true/false)?
- Is frontmatter valid YAML?
- If present, is `kicker` a short text label such as `Course` or `Module 1`?
- Are fill-blank blanks using single braces `{word}` for known answers or `____` for open answers?

## Final parser gate

Run parser validation before publishing examples or generated output.
If parser output differs from expected structure, fix syntax first before adding more content.

Parser errors to watch for:

- Unmatched `close:` (no matching open container)
- Assessment options with no heading above them
- Frontmatter with unclosed `---` block
- YAML syntax errors (unquoted colons, bad indentation)
