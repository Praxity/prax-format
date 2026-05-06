# Assessment Blocks

> **`as: choice` dispatches on marker type.** Both choose-one and choose-many use the same `as: choice` value. The parser auto-detects which type to create based on the option marker:
> - `(x)` / `( )` round-bracket markers → single-choice (choose-one)
> - `[x]` / `[ ]` square-bracket markers → multi-choice (choose-many)

## choose-one

Single-answer question. Use round-bracket `( )` option markers.

**Syntax:**
```prax
### Which action is safest first?
as: choice
points: 2

(x) Stop work and isolate hazard
feedback: Correct — isolating the hazard prevents further injury.
( ) Keep working and monitor
feedback: Monitoring alone does not remove the risk.
( ) Ask later
```

Per-option `feedback: <text>` lines go directly after each option, not indented. A global `feedback:` line (not after an option) sets fallback feedback for the whole question.

## choose-many

Multi-answer question. Use square-bracket `[ ]` option markers.

**Syntax:**
```prax
### Select all mandatory checks
as: choice
shuffle: true

[x] PPE verified
feedback: Required before any task begins.
[x] Exit route clear
[ ] Phone battery full
feedback: Not a mandatory safety check.
```

## match

Matching pairs.

**Syntax:**
```prax
### Match hazard to control
as: match

Fall risk :: Guardrail
Electrical fault :: Lockout/tagout
```

## order

Ordered sequence question.

**Syntax:**
```prax
### Put the steps in order
as: order

1. Identify
2. Assess
3. Control
```

## free-response

Open response.

**Syntax:**
```prax
### Describe one improvement
as: free-response
required: true

Mention one process and one communication change.
```

## fill-blank

Fill-in-the-blank content.

**Syntax:**
```prax
### Complete the line
as: fill-blank

Verify {pressure} and inspect {seal} before use.
```

## hotspot

Image hotspot assessment.

**Syntax:**
```prax
/assets/diagram.png
as: hotspot
alt: Labeled safety diagram
spot: Valve; 30%; 15%
spot: Release pin; 45%; 25%; correct
```

> **Important:** All parameters — including `spot:` lines — must be contiguous with no blank lines between them. `collectParams` stops at the first blank line, so any `spot:` entries after a blank line are silently dropped.

## rate

Rating scale / Likert assessment.

> **`as:` value is `rating`.** The manifest keyword for this block type is `rate`, but the parser's `isAssessmentAs()` function only accepts `as: rating`. Using `as: rate` will not be recognized as an assessment block.

**Syntax:**
```prax
### Confidence with emergency protocol
as: rating

1: Not confident
2: Somewhat confident
3: Confident
4: Very confident
5: Expert
```

## categorize

Category sorting.

**Syntax:**
```prax
### Sort by category
as: categorize
shuffle: true

Engineering Controls:
- Machine guard
- Ventilation

PPE:
- Gloves
- Goggles
```

## assessment-group

Group multiple assessments. `as: assessment-group` conventionally goes on a `##` heading (H2), which matches how the parser closes implicit groups at heading-level boundaries.

**Syntax:**
```prax
## Final Checkpoint
as: assessment-group
mode: all
showResultsSummary: true
passingScore: 80

### Q1
as: choice

(x) Correct
( ) Incorrect

close: assessment-group
```

Note: `passingScore:` is the correct parameter name (not `passing:`).

## Shared scoring parameters

| Parameter | Type |
|---|---|
| `points` | number |
| `required` | boolean |
| `timed` | number |
| `pass` | number |
| `attempts` | number |
| `shuffle` | boolean (supported by choice/match/order/categorize) |

## Decorator parameters

| Parameter | Type |
|---|---|
| `scored` | boolean |
| `competency` | text |
| `confidence` | boolean |
| `retrieval` | boolean |
| `feedback` | enum/text |

## Variant summary from manifest

- choose-one: `radio` implemented; others planned.
- choose-many: `checkbox` implemented; others planned.
- match: `drag-drop` implemented.
- order: `drag-sort` implemented.
- free-response: `textarea` implemented.
- hotspot: `click-regions` implemented.
- rate: `likert` implemented.
- fill-blank: `inline-inputs` implemented.
- assessment-group mode: `all | oneOf`.
