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

correct: Well done. Isolation is always the first action.
incorrect: Review the emergency response procedure before continuing.
```

Per-option `feedback: <text>` lines go directly after each option, not indented. These are shown for the specific option the learner selected.

Whole-question feedback lines go after all options (not after any individual option):
- `correct: <text>` — shown when the learner answers the question correctly overall.
- `incorrect: <text>` — shown when the learner answers incorrectly overall.

Both `correct:`/`incorrect:` and per-option `feedback:` can be used together. They are stored as `data.correct` and `data.incorrect` on the block.

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
question: Select the release pin.
alt: Labeled safety diagram
spot: Valve; 30%; 15%
spot: Release pin; 45%; 25%; correct
```

Use `question:` for the learner-facing assessment title/stem. `alt:` is only the image alternative text.

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

## matrix

Matrix-style Likert assessment. The heading is the matrix question, numbered `N: label` lines define the scale, and list items define the statements.

**Syntax:**
```prax
### How confident are you?
as: matrix

1: Not at all confident
2: Slightly confident
3: Moderately confident
4: Very confident
5: Extremely confident

- Using PPE correctly
- Reading warning labels
- Following emergency procedures
```

Shorter scales are valid:

```prax
### How confident are you?
as: matrix

1: Not confident
2: Somewhat confident
3: Confident

- Using PPE correctly
- Reading warning labels
- Following emergency procedures
```

Consecutive `as: matrix` headings are also collapsed into one table-style matrix assessment.

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

Group multiple assessments. `as: assessment-group` conventionally goes on a `##` heading (H2), which matches how the parser closes implicit groups at heading-level boundaries. Each assessment inside the group uses a `###` heading (H3) as its question title, since the group itself uses a `##` heading (H2).

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

**`mode: oneOf`** allows assessment choice — the learner picks which question(s) to answer rather than completing all of them. For example, in a group of 5 questions with `mode: oneOf`, the learner can choose any single question to answer.

Note: `passingScore:` is the correct parameter name (not `passing:`).

**Parameters:**

| Parameter | Type | Description |
|---|---|---|
| `mode` | `all \| oneOf` | `all` (default): learner must answer every question. `oneOf`: learner chooses which to answer. |
| `passingScore` | number | Minimum score (0–100) to pass the group. |
| `showResultsSummary` | boolean | Show a results summary after all questions are answered. |
| `pointsOverride` | number | Override total point value for the group (instead of summing individual question points). |
| `requireAll` | boolean | Whether all questions must be attempted before submitting. |

## Shared scoring parameters

These parameters are available on all assessment types:

| Parameter | Type | Description |
|---|---|---|
| `points` | number | Point value awarded for a correct answer. Used in scored assessment groups and SCORM/xAPI reporting. |
| `required` | boolean | Whether the learner must answer this question before proceeding. |
| `timed` | number | Time limit in seconds. When set, a countdown timer appears. |
| `pass` | number | Minimum score (0–100) required to pass this individual question. |
| `attempts` | number | Maximum number of attempts allowed before the answer is locked. |
| `shuffle` | boolean | Randomize option/item order on each attempt. Available on: choose-one, choose-many, match, order, categorize. |

## Decorator parameters

Decorator parameters add metadata for learning analytics and adaptive behavior:

| Parameter | Type | Description |
|---|---|---|
| `scored` | boolean | Whether this assessment contributes to the overall course score. Default: `true` for assessments inside an assessment-group. |
| `competency` | text | Competency tag or identifier this question maps to (e.g. `"fire-safety"`). Used in xAPI reporting. |
| `confidence` | boolean | Enables confidence-based marking — the learner rates how sure they are of their answer. |
| `retrieval` | boolean | Marks this as a retrieval practice question (spaced repetition). Affects analytics reporting. |
| `feedback` | enum | Feedback display mode. Controls when and how feedback is shown to the learner. |

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
