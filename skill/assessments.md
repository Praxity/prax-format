# Assessments — .prax Sub-Skill

## Assessment syntax overview

In grammar v3, assessments are written as a heading with an `as:` value. The heading text becomes the question. Options, pairs, and scale items follow in the body.

```prax
### Question text
as: choice

(x) Correct option
( ) Incorrect option
```

Key rules:

- Use `as: choice` for both choose-one and choose-many. The parser dispatches on marker type, not a separate `as:` value.
- Choose-one markers use parentheses: `(x)` and `( )`.
- Choose-many markers use brackets: `[x]` and `[ ]`.
- The manifest keyword for the rating block is `rate`, but the parser `as:` value is `rating` — not `rate`.
- Feedback for an option is written on the next line with `feedback: <text>` — flush to the left margin, not indented.
- Block-level feedback (correct/incorrect) uses `correct: <text>` and `incorrect: <text>` on their own lines.

## as: choice — dispatching

Both choose-one and choose-many use `as: choice`. The parser determines the question type from the first option marker it encounters:

- If the first marker is `(x)` or `( )` → single-choice (choose-one)
- If the first marker is `[x]` or `[ ]` → multiple-choice (choose-many)

Do **not** use `as: choose-one` or `as: choose-many` — these values are not accepted by the parser.

## Shared assessment parameters

These parameters are available across all assessment blocks.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `scored` | boolean | false | Marks block for scoring/tracking |
| `competency` | text | — | Competency tag for reporting *(not applied yet)* |
| `confidence` | boolean | false | Captures learner confidence *(not applied yet)* |
| `retrieval` | boolean | false | Tags block as retrieval practice *(not applied yet)* |
| `feedback` | enum/text | — | Feedback mode or global message |
| `points` | number | — | Points awarded for correct answer |
| `required` | boolean | false | Must be completed before continuing |
| `timed` | number | — | Time budget in seconds *(not applied yet)* |
| `attempts` | number | — | Maximum attempts allowed |
| `layout` | enum | — | `wide`, `full`, `breakout` |

> **Not applied yet.** `competency`, `confidence`, `retrieval` and `timed` parse and
> validate, so a course using them stays valid, but nothing reads them and they make
> no difference to exported output.
>
> A pass threshold is a group-level concept: use `assessment-group` with
> `passingScore`. There is no per-question `pass`.


`shuffle` is additionally available for: choose-one, choose-many, match, order, categorize.

## choose-one

Keyword: `choose-one`. Parser `as:` value: `choice`. Markers: `(x)` correct, `( )` incorrect.

```prax
### Which control comes first?
as: choice
scored: true
points: 2
required: true
shuffle: true

(x) Eliminate the hazard
feedback: Correct. Elimination removes the root cause.
( ) Add warning labels only
feedback: Labels help, but they do not remove the hazard.
( ) Assume PPE is sufficient
feedback: PPE is the last resort, not the first control.
```

Block-level feedback (applies regardless of which option is chosen):

```prax
### What is the first step when you see a spill?
as: choice
scored: true

(x) Stop work and isolate the area
( ) Continue working carefully
( ) Call for a supervisor first

correct: Well done. Isolation prevents further harm.
incorrect: Review the emergency spill response procedure.
```

### choose-one parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `shuffle` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |
| `style` | enum | `radio` | radio |

Planned style variants: `card-select`, `image-select`, `inline-dropdown`.

## choose-many

Keyword: `choose-many`. Parser `as:` value: `choice`. Markers: `[x]` correct, `[ ]` incorrect.

```prax
### Select all required pre-task checks
as: choice
scored: true
points: 3
shuffle: true

[x] Equipment lockout confirmed
feedback: Correct. Energy isolation is mandatory.
[x] PPE inspected
feedback: All PPE must be confirmed before starting.
[ ] Music volume increased
feedback: Not a safety requirement.
[x] Emergency exits clear
feedback: Exits must always be unobstructed.
```

### choose-many parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `shuffle` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |
| `style` | enum | `checkbox` | checkbox |

Planned style variants: `grid`, `tag-picker`.

## match

Use `as: match` with `left :: right` pairs. Each pair is on its own line.

```prax
### Match the risk with the control
as: match
scored: true
points: 4
shuffle: true

Chemical splash :: Face shield
Fall risk :: Guard rail
Electrical fault :: Lockout/tagout
Noise exposure :: Hearing protection
```

### match parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `shuffle` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |
| `style` | enum | `select-dropdowns` | select-dropdowns |

Matching renders as labelled native select controls; the browser owns keyboard interaction, popup
state, option movement, selection, and dismissal. Planned style variant: `line-drawing`.

## order

Use `as: order` with numbered items. Lines start with `1.`, `2.`, etc.

```prax
### Put the response steps in order
as: order
scored: true
points: 4
shuffle: true

1. Stop work
2. Secure area
3. Notify lead
4. Document incident
```

### order parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `shuffle` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |
| `style` | enum | `up-down-arrows` | up-down-arrows |

Ordering renders with accessible move-up and move-down controls. Planned style variant: `numbered-input`.

## free-response

Use `as: free-response`. The heading is the question; `description:` adds visible instructions.

```prax
### Describe one safety improvement for your team
as: free-response
scored: true
points: 5
required: true
attempts: 1
buttonLabel: Submit response
description: Name one process change and one communication change.
placeholder: Type your response
downloadAs: both
```

Keep placeholders brief. Bare body text remains supported as legacy placeholder shorthand, but
visible instructions should use the heading and `description:` so they remain available while the
learner writes.

### free-response parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |
| `description` | text | visible supporting instructions | — |
| `placeholder` | text | brief input hint | Type your answer |
| `buttonLabel` | text | visible action text | Complete reflection (ungraded), Submit (graded) |
| `downloadAs` | enum | `txt` \| `docx` \| `both` | — |
| `private` | boolean | true / false | false |
| `style` | enum | `textarea` | textarea |

Use `private: true` for a personal reflection that must stay on the learner's device. Omit it
for a response that should be submitted to the LMS. Private responses are never included in
SCORM or xAPI interaction data.

Planned style variants: `rich-text`, `recording`.

## fill-blank

Use `as: fill-blank`. `{word}` creates an input with a configured correct answer. A run of
four or more underscores (`____`) creates an open input with no predefined answer.

```prax
### Complete the safety procedure
as: fill-blank
scored: true
points: 2

Before entering the lab, verify {ventilation} and review the {procedure} sheet.
Workers must wear {gloves} and {eye protection} at all times.
```

Note: Use `{word}` — single braces — not double braces or angle brackets.
Use `____` when the author intentionally does not supply a correct answer. The surrounding
sentence is rendered with bottom-rule inputs inline. Known answers size the field to the
expected answer length (clamped to 8–32 characters); open blanks use a 12-character default.

With `scored: true`, all known answers must match. Comparison is case-insensitive, trims
surrounding whitespace, collapses internal whitespace, accepts canonically equivalent Unicode,
and accepts alternatives present in imported course data. Punctuation and accents remain
significant; fuzzy and edit-distance matching are not used. Learners see correct/incorrect status
for each known blank after submission, never the expected answer.

Open `____` blanks do not affect correctness. They still count when a required mixed activity is
checked for completeness. An all-open activity is completion-only even if `scored: true`; it emits
no score, is excluded from assessment-group percentages and scored SCORM interactions, and is
flagged by the editor as an authoring issue.

### fill-blank parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |
| `style` | enum | `inline-inputs` | inline-inputs |

Planned style variants: `word-bank`, `dropdown`.

## hotspot

Use an image path followed by `as: hotspot` and repeated `spot:` params. Each spot is `label; x%; y%; [correct]`.

```prax
/assets/extinguisher-diagram.png
as: hotspot
question: Select the pull pin.
alt: Extinguisher diagram with labeled parts
scored: true
points: 4

spot: Pressure gauge; 28%; 18%
spot: Pull pin; 42%; 25%; correct
spot: Discharge horn; 65%; 40%
```

### hotspot parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `question` | string | learner-facing prompt | — |
| `alt` | string | descriptive text | — |
| `scored` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |
| `style` | enum | `click-regions` | click-regions |

Planned style variants: `labeled-diagram`, `description-list`.

## rate (as: rating)

Manifest keyword: `rate`. Parser `as:` value: `rating` — **not** `rate`. Use numbered scale lines in `N: label` format.

```prax
### How confident are you with incident reporting?
as: rating
required: true

1: Not confident
2: Somewhat confident
3: Confident
4: Very confident
5: Expert
```

Common mistake: writing `as: rate` — the parser only accepts `as: rating`.

### rate parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |
| `style` | enum | `likert` | likert |

Planned style variants: `stars`, `slider`.

## matrix

Use `as: matrix` for a table-style Likert assessment. The heading is the matrix question, numbered `N: label` lines define the scale, and list items define the statements.

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

Consecutive `as: matrix` headings are also collapsed into one matrix assessment.

## categorize

Use `as: categorize`. Categories are defined with `Category Name:` headers, items follow as `- item` list entries.

```prax
### Sort items by PPE category
as: categorize
scored: true
points: 4
shuffle: true

Head Protection:
- Hard hat
- Bump cap

Eye Protection:
- Safety goggles
- Face shield

Hand Protection:
- Nitrile gloves
- Cut-resistant gloves
```

### categorize parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `shuffle` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `attempts` | number | integer | — |

## assessment-group

Use `as: assessment-group` on a heading to wrap multiple assessments. The group requires an explicit `close: assessment-group`.

```prax
## Module Checkpoint
as: assessment-group
mode: all
showResultsSummary: true
passingScore: 80
requireAll: true

### Which action is safest first?
as: choice
scored: true

(x) Stop work
feedback: Correct. Stopping prevents harm from escalating.
( ) Continue and monitor
feedback: Monitoring alone is not sufficient.

### Match hazard to control
as: match
scored: true

Noise exposure :: Hearing protection
Slip risk :: Housekeeping

### Rate your confidence on hazard identification
as: rating

1: No confidence
2: Some confidence
3: Confident

close: assessment-group
```

### assessment-group parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `mode` | enum | `all`, `oneOf` | all |
| `showResultsSummary` | boolean | true / false | false |
| `pointsOverride` | number | any positive number | — |
| `passingScore` | number | 0–100 | — |
| `requireAll` | boolean | true / false | false |
| `buttonLabel` | text | visible action text | Check all (ungraded), Submit all (graded) |
| `layout` | enum | `wide`, `full`, `breakout` | — |

Note: The parameter is `passingScore`, not `passing` or `pass-score`.

Group progress counts committed member completions. When every member is complete, the group action
is removed and the localized result is announced.

## Feedback patterns

Feedback attaches to individual options or to the block as a whole.

Auto-scored work always shows and announces a localized **Correct** or **Incorrect** result, even
when no feedback was authored. Authored feedback is optional and additive. Completion-only work
announces **Completed** instead of a score or correctness result.

### Per-option feedback

Write `feedback: <text>` on the line immediately after the option, flush to the left margin (not indented):

```prax
### What is the safest action?
as: choice

(x) Isolate energy source
feedback: Correct. Isolation prevents escalation.
( ) Wait for supervisor approval first
feedback: Approval matters, but immediate isolation comes first.
( ) Resume work with caution
feedback: Work must stop entirely until isolation is confirmed.
```

The same pattern works for choose-many:

```prax
### Select all valid emergency response actions
as: choice
scored: true

[x] Activate alarm
feedback: Always trigger the alarm immediately.
[x] Evacuate to muster point
feedback: Proceed to the designated assembly area.
[ ] Stay and contain the fire
feedback: Firefighting is for trained personnel only.
```

### Block-level feedback

Use `correct:` and `incorrect:` to attach feedback that applies to the outcome of the whole block:

```prax
### What must you do before starting equipment?
as: choice
scored: true

(x) Complete a pre-start inspection
( ) Start immediately if no one is watching
( ) Ask a co-worker to check for you

correct: Pre-start inspection is mandatory per operating procedure.
incorrect: Review section 3 of the safety manual before continuing.
```

## Quick validation checklist

- Did choose-one use `(x)`/`( )` and choose-many use `[x]`/`[ ]`?
- Did every assessment heading start with a heading line (`### ...`)?
- Is the `as:` value `choice` for both choose-one and choose-many (not `as: choose-one`)?
- Is the `as:` value `rating` for the rate block (not `as: rate`)?
- Is per-option `feedback:` on its own line, flush left, immediately after the option it belongs to?
- Are `correct:` and `incorrect:` placed after all options (not after any individual option)?
- Does every `assessment-group` end with `close: assessment-group`?
- Is `passingScore` used (not `passing` or `pass-score`) for assessment-group?
- Are `points`, `attempts`, and `timed` numeric?
- Are `shuffle` and `required` boolean (`true`/`false`)?
- Are fill-blank blanks using `{word}` for known answers or `____` for open answers?
