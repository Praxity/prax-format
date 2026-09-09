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

A whole-assessment `feedback: <text>` line is also accepted as a shared fallback if `correct:` or `incorrect:` is not provided. Put it with the question parameters before the options, or separate it from the last option with a blank line. A line directly after an option belongs to that option.

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

### Reveal answers after submission

For choice, match, order, fill-blank, and categorize questions,
`feedbackMode: reveal` marks the submitted response and generates a correct-answer
list in the feedback area after one submission. Choice also identifies every
correct option in its label.
No authored answer explanation is required. An optional `incorrect:` pointer
appears separately from the generated answers.

```prax
### Which action comes first?
as: choice
feedbackMode: reveal
required: true

( ) Continue working
(x) Stop and assess the hazard

incorrect: Review the safety procedure.
```

The attempt locks, no Try again action appears, and the question counts as
completed even when the answer is incorrect. A scored question still awards zero
points for an incorrect answer. A separate assessment-group passing threshold
continues to use the actual score. Reveal mode overrides `attempts:` with one
attempt. Reloading preserves the submitted response and revealed answers; resetting
learner progress clears them.

`feedbackMode: retry` is the default and retains the existing attempt and feedback
behavior. Reveal mode requires configured correct answers. Other assessment types
report a validation error.

Generated answers follow the authored order. Match lists each prompt with its
correct match; order lists the correct sequence; fill-blank numbers the expected
answers by blank position; categorize lists each item with its category. The submitted responses stay
visible and cannot be changed. Put optional `incorrect:` pointers with the
parameters before the body for non-choice questions.

## match

Matching pairs.

**Syntax:**
```prax
### Match hazard to control
as: match

Fall risk :: Guardrail
Electrical fault :: Lockout/tagout
```

Matching renders with labelled native select controls. The browser owns their keyboard interaction,
open state, option movement, selection, and dismissal.

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

Ordering renders with accessible move-up and move-down controls rather than drag-only interaction.

## free-response

Open response.

**Syntax:**
```prax
### Describe one improvement
as: free-response
required: true
buttonLabel: Keep this in mind
description: Mention one process and one communication change.
placeholder: Type your reflection
downloadAs: both
private: true
```

`buttonLabel` overrides the visible action text. An ungraded free response defaults to
**Complete reflection**; a graded response defaults to **Submit**.
Keep the question and supporting instructions visible in the heading and `description`. Use
`placeholder` only for a brief input hint; bare body text remains supported as legacy placeholder
shorthand.
`downloadAs` is optional. Use `txt`, `docx`, or `both` to let the learner download their response;
omit it when no download controls are needed.
Use `private: true` for a personal reflection that must not be sent to the LMS. By default,
free responses are submitted as neutral (unscored) interactions.

## fill-blank

Fill-in-the-blank content.

**Syntax:**
```prax
### Complete the line
as: fill-blank

Verify {pressure} and inspect {seal} before use.
```

Use `{answer}` when the activity has a configured correct answer.
Separate accepted alternatives with `|`, for example `{colour|color}`.
Prefix an alternative with `~` to use wildcards: `{permit|~entry*permit}`.
Within a wildcard pattern, `*` matches zero or more characters and `?` matches
exactly one character. The pattern must match the whole answer. Other characters
are literal; raw regular expressions are not supported. Without `~`, `*` and `?`
are literal too. Blank responses never count as correct.
The first alternative is the answer shown by `feedbackMode: reveal`, so put a
readable example first and wildcard patterns after it. Empty alternatives are ignored.
The `|` character separates alternatives and cannot appear inside a single answer.

Use `____`
for an open blank with no predefined answer:

```prax
### Finish the sentence
as: fill-blank

Today I will ____.
```

The authored sentence is preserved and each marker renders as an inline, bottom-rule text input.
Known answers size the field to the expected answer length (clamped to 8–32 characters);
open `____` blanks use a 12-character default.

When `scored: true`, every known-answer blank must match its configured answer (or an
authored alternative). Matching is case-insensitive, trims surrounding
whitespace, collapses internal whitespace, and treats canonically equivalent Unicode text as the
same. It does not ignore punctuation or accents and does not use fuzzy or edit-distance matching.
After submission, each known-answer blank is marked correct or incorrect without revealing the
answer.

Open `____` blanks have no correct answer. In a mixed activity they must still be completed when
the question is required, but only known-answer blanks affect correctness. If every blank is open,
the activity is completion-only even when `scored: true`: it emits no score, is excluded from
assessment-group percentages and scored SCORM interactions, and the editor reports the scoring
configuration as an authoring issue.

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

The heading form every other assessment uses also works, with the image on the line after the params:

```prax
### Select the release pin.
as: hotspot

/assets/diagram.png
alt: Labeled safety diagram
spot: Valve; 30%; 15%
spot: Release pin; 45%; 25%; correct
```

> **Important:** All parameters — including `spot:` lines — must be contiguous with no blank lines between them. `collectParams` stops at the first blank line, so any `spot:` entries after a blank line are silently dropped.

**Spot syntax:**

```prax
spot: Label; x%; y%; correct
```

- `Label` is the learner-visible hotspot label.
- `x` and `y` are percentages across and down the image.
- `correct` marks the spot as a correct selection.

Authored `.prax` hotspot spots currently render as small elliptical click regions. Renderer-internal hotspot shapes, multi-select modes, custom indicators, and per-spot feedback are not yet exposed as public `.prax` syntax.

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

`style: likert` is the default and shows the authored scale labels.
`style: stars` shows star choices. `style: slider` shows a native slider with the
selected value and label. Rating is an unscored response; `required: true` requires
an interaction before completion. `display: standard|scenario` has no effect on
rating and is not offered in authoring suggestions. Legacy `numeric` and `emoji`
styles remain supported.

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

Categories can include an image before their items:

```prax
### Sort PPE by body area
as: categorize

Head:
image: /assets/hard-hat.png
alt: Hard hat
- Hard hat
- Bump cap

Eyes:
image: /assets/goggles.png
alt: Safety goggles
- Goggles
- Face shield
```

## assessment-group

Serialization preserves the group title, settings, authored IDs, and member
references. It writes `close: assessment-group` after the last member so the next
question remains outside the group. Nested assessment headings retain their
authored level so they remain inside their parent container.

Group multiple assessments. Conventionally, put `as: assessment-group` on a `##` heading and use `###` headings for its questions. Ordinary H2–H4 headings do not end the group. Use `close: assessment-group` before following content on the same page; a page break, H1 heading, or the end of the file also closes it.

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

**`mode: oneOf`** allows assessment choice — the learner selects one question to answer. For example, in a group of 5 questions with `mode: oneOf`, the learner can choose any single question to answer.

Note: `passingScore:` is the correct parameter name (not `passing:`).

**Parameters:**

| Parameter | Type | Description |
|---|---|---|
| `mode` | `all \| oneOf` | `all` (default): learner must answer every question. `oneOf`: learner chooses which to answer. |
| `passingScore` | number | Minimum score (0–100) to pass the group. |
| `showResultsSummary` | boolean | Show a results summary after all questions are answered. |
| `pointsOverride` | number | Override total point value for the group (instead of summing individual question points). |
| `requireAll` | boolean | Whether all questions must be attempted before submitting. |
| `buttonLabel` | text | Override the visible group action text. |

A member question can use `name:` as a logic anchor and `id:` as a durable identity. These parameters do not change its group membership or shared submit action.

Members inherit the group’s `layout` (`wide`, `full`, or `breakout`) unless they specify their own layout. Published pages apply this layout before interaction initializes, so activation preserves question widths.

Group progress counts the same committed question results that mark each member complete. After the required
members are complete, the group action is removed and the localized result is announced. In
`mode: all`, ungraded groups default to **Check all** and graded groups to **Submit all**.
In `mode: oneOf`, only the selected member is shown, submitted, and counted toward the result;
the defaults are **Check selected** and **Submit selected**.

## Shared scoring parameters

These parameters are available on all assessment types:

Published assessments and learner activities use one neutral surface with a 1px semantic border.
This activity material is distinct from tinted callouts and unfilled quotes and applies to
standalone questions, grouped assessments, checklists, ratings, and signatures.

| Parameter | Type | Description |
|---|---|---|
| `points` | number | Point value awarded for a correct answer. Used in scored assessment groups and SCORM/xAPI reporting. |
| `required` | boolean | Whether the learner must answer this question before proceeding. |
| `timed` | number | Intended time limit in seconds. **Not applied yet** — no countdown is rendered. |
| `attempts` | number | Maximum number of attempts before the answer is locked. Use `0` for unlimited attempts. |
| `shuffle` | boolean | Randomize option/item order on each attempt. Available on: choose-one, choose-many, match, order, categorize. |

Submitting auto-scored work always shows and announces a localized **Correct** or **Incorrect**
status, even when no feedback was authored. Authored feedback is optional and appears after that
status. Completion-only work announces **Completed** instead of a score or correctness result.

## Decorator parameters

Decorator parameters add metadata for learning analytics and adaptive behavior:

| Parameter | Type | Description |
|---|---|---|
| `scored` | boolean | Whether this assessment contributes to the overall course score. Default: `true` for assessments inside an assessment-group. |
| `competency` | text | Competency tag or identifier this question maps to (e.g. `"fire-safety"`). **Not applied yet** — not emitted in xAPI. |
| `confidence` | boolean | Intended to enable confidence-based marking. **Not applied yet** — no confidence prompt is rendered. |
| `retrieval` | boolean | Marks this as a retrieval practice question. **Not applied yet** — does not affect analytics. |
| `feedback` | text | Shared authored feedback when no `correct:` or `incorrect:` text is supplied. Legacy mode words `immediate`, `after-submit`, `after-all`, `never`, and `deferred` do not configure feedback timing in published output. |
| `feedbackMode` | enum | `retry` or `reveal` for choice, match, order, fill-blank, and categorize. See reveal behavior above. |
| `description` | text | Supporting context shown between the question and response controls. |
| `display` | enum | `standard` or `scenario`. Use `scenario` when a concise question needs longer context in the same assessment surface. |

## Variant summary from manifest

- choose-one: `radio` implemented; others planned.
- choose-many: `checkbox` implemented; others planned.
- match: labelled native select matching implemented.
- order: accessible move controls implemented.
- free-response: `textarea` implemented.
- hotspot: `click-regions` implemented.
- rating: `likert`, `stars`, and `slider` implemented.
- matrix: `likert` implemented.
- fill-blank: `inline-inputs` implemented.
- assessment-group mode: `all | oneOf`.
