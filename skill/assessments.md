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
| `competency` | text | — | Competency tag for reporting |
| `confidence` | boolean | false | Captures learner confidence |
| `retrieval` | boolean | false | Tags block as retrieval practice |
| `feedback` | enum/text | — | Feedback mode or global message |
| `points` | number | — | Points awarded for correct answer |
| `required` | boolean | false | Must be completed before continuing |
| `timed` | number | — | Time budget in seconds |
| `pass` | number | — | Pass threshold (percentage) |
| `attempts` | number | — | Maximum attempts allowed |
| `layout` | enum | — | `wide`, `full`, `breakout` |

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
| `pass` | number | percentage | — |
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
| `pass` | number | percentage | — |
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
| `pass` | number | percentage | — |
| `attempts` | number | integer | — |
| `style` | enum | `drag-drop` | drag-drop |

Planned style variants: `select-dropdowns`, `line-drawing`.

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
| `pass` | number | percentage | — |
| `attempts` | number | integer | — |
| `style` | enum | `drag-sort` | drag-sort |

Planned style variants: `numbered-input`, `up-down-arrows`.

## free-response

Use `as: free-response`. The heading is the question. Optional prompt text appears in the body.

```prax
### Describe one safety improvement for your team
as: free-response
scored: true
points: 5
required: true
attempts: 1

Name one process change and one communication change.
```

### free-response parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `pass` | number | percentage | — |
| `attempts` | number | integer | — |
| `style` | enum | `textarea` | textarea |

Planned style variants: `rich-text`, `recording`.

## fill-blank

Use `as: fill-blank`. Blanks are wrapped in single braces: `{word}`. Each brace creates one input.

```prax
### Complete the safety procedure
as: fill-blank
scored: true
points: 2

Before entering the lab, verify {ventilation} and review the {procedure} sheet.
Workers must wear {gloves} and {eye protection} at all times.
```

Note: Use `{word}` — single braces — not double braces or angle brackets.

### fill-blank parameters

| Parameter | Type | Valid values | Default |
|---|---|---|---|
| `scored` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `pass` | number | percentage | — |
| `attempts` | number | integer | — |
| `style` | enum | `inline-inputs` | inline-inputs |

Planned style variants: `word-bank-drag`, `dropdown`.

## hotspot

Use an image path followed by `as: hotspot` and repeated `spot:` params. Each spot is `label; x%; y%; [correct]`.

```prax
/assets/extinguisher-diagram.png
as: hotspot
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
| `alt` | string | descriptive text | — |
| `scored` | boolean | true / false | false |
| `points` | number | any positive number | — |
| `required` | boolean | true / false | false |
| `timed` | number | seconds | — |
| `pass` | number | percentage | — |
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
| `pass` | number | percentage | — |
| `attempts` | number | integer | — |
| `style` | enum | `likert` | likert |

Planned style variants: `stars`, `slider`.

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
| `pass` | number | percentage | — |
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
| `layout` | enum | `wide`, `full`, `breakout` | — |

Note: The parameter is `passingScore`, not `passing` or `pass-score`.

## Feedback patterns

Feedback attaches to individual options or to the block as a whole.

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
- Is `feedback:` on its own line, flush left, immediately after the option?
- Does every `assessment-group` end with `close: assessment-group`?
- Is `passingScore` used (not `passing` or `pass-score`) for assessment-group?
- Are `points`, `pass`, `attempts`, and `timed` numeric?
- Are `shuffle` and `required` boolean (`true`/`false`)?
- Are fill-blank blanks using `{word}` single braces?
