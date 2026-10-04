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
feedback: Correct. Isolating the hazard prevents further injury.
( ) Keep working and monitor
feedback: Monitoring alone does not remove the risk.
( ) Ask later

correct: Well done. Isolation is always the first action.
incorrect: Review the emergency response procedure before continuing.
```

Per-option `feedback: <text>` lines go directly after each option, not indented. These are shown for the specific option the learner selected.

Whole-question feedback lines go after all options (not after any individual option):
- `correct: <text>`, shown when the learner answers the question correctly overall.
- `incorrect: <text>`, shown when the learner answers incorrectly overall.

Both `correct:`/`incorrect:` and per-option `feedback:` can be used together. They are stored as `data.correct` and `data.incorrect` on the block.

A whole-assessment `feedback: <text>` line is also accepted as a shared fallback if `correct:` or `incorrect:` is not provided. Put it with the question parameters before the options, or separate it from the last option with a blank line. A line directly after an option belongs to that option.

## choose-many

Multi-answer question. Use square-bracket `[ ]` option markers.

For both choice types, selecting an option's text, row padding, or empty space
around its native control selects that option. Choice rows remain stationary
while pressed so the same target receives pointer-down and pointer-up.

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


### Scoring retries

Standalone assessments reserve space for Check or Submit before interaction while
keeping the existing rule for when that action becomes available. Choice questions
reserve that space before JavaScript activates, without exposing an inactive action.
Group members leave submission space to the shared group action. Empty feedback
adds no gap before or after activation. After submission,
the result appears before the attempt count and Try again. Submitted answers remain
readable and cannot be edited, including matrix and likert selections. Try again
re-enables the response and returns keyboard focus to its first enabled control.
Inline-only fill-blank results focus a brief review summary above the retry action.
These presentation changes do not alter authored syntax, scoring or attempt limits.

The course score uses the latest submitted scored attempt for each question, not
an average of that question's attempts. An incorrect attempt followed by a correct
retry contributes the correct retry's score. Earlier attempts remain in LMS
interaction history. Ungraded survey responses do not contribute to the course score.

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
After a keyboard move, focus stays on the moved item's control. At either end, focus moves
to that item's remaining move button. A polite announcement gives the new position without
revealing whether the order is correct before submission.

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

### Dropdown blanks

Use `style: dropdown` to give each blank its own choices. Prefix each correct
choice with `*`; choices without `*` are distractors. Choices stay in authored
order, so the correct answer need not be first. Mark more than one choice when
several answers are accepted. Each blank needs at least two distinct choices
and at least one marked answer.

```prax
### Complete the description
as: fill-blank
style: dropdown

The sky is {red|*blue|green} and grass is {*green|purple|orange}.
```

### Shared word bank

Use `style: word-bank` with a pipe-separated `bank:`. Each bank entry can be used
once. Repeat an entry to make it available more than once. Include distractors
in the bank without using them as blank answers.

```prax
### Complete the pattern
as: fill-blank
style: word-bank
bank: red | blue | red | green

The pattern is {red}, {blue}, then {red}.
```

The bank must contain enough copies of each primary answer, even if a blank
also accepts alternatives such as `{blue|green}`. Every alternative must appear
in the bank. Answers are literal, so `~` does not enable wildcard matching.
These two styles do not support open `____` blanks. A choice that contains `|` or
braces, or a dropdown choice that starts with `*`, needs the backslash escapes in
[literal answer punctuation](#literal-answer-punctuation). A dropdown cannot have
empty or duplicate choices.

Author these styles and `bank:` on each question; course-wide block defaults do not set them.

Both styles use native select controls. Clearing or changing a bank selection
makes its previous entry available again. A word bank is selected from menus,
not dragged. Submission, scoring, retry, answer reveal and saved progress follow
the usual fill-blank rules. Without `style:`, or with `style: inline-inputs`,
`{answer|alternative}` continues to mean accepted typed answers.

### Literal answer punctuation

A backslash makes the next delimiter character part of an answer or choice. Use it
when an answer contains `|`, braces, or a leading `*`, `~` or `?` that should not act
as syntax.

- `{a\|b}` accepts the answer `a|b`.
- `{\{value\}}` accepts `{value}`.
- `{\~0.5}` accepts the literal answer `~0.5` instead of starting a wildcard pattern.
- Inside a `~` pattern, `\*` and `\?` match a literal `*` or `?`: `{~a\*b}` accepts
  only `a*b`. Unescaped `*` and `?` stay wildcards.
- In a dropdown, `{*\*star|plain}` marks the choice `*star` correct. The first `*`
  marks the answer; `\*` is part of the label. An escaped star on its own does not
  mark a choice correct.
- Word-bank entries use the same escapes: `bank: a\|b | \{value\} | C:\\temp`.

Outside a blank, `\{literal\}` and `\____` print as text and create no blank.

Answers and choices are plain text, so inline formatting does not apply to them.
Dropdown and word-bank answers always match the exact choice, even one that starts
with `~`.

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

Use `question:` for the learner-facing assessment title/stem. `alt:` describes the image.
Older hotspots without a question still work. Their response group uses the image
description as its accessible name, and learner output omits the authoring placeholder
"No question text". Required status and scoring stay the same. A meaningful image
description remains necessary; this fallback does not supply missing alt text.

The heading form every other assessment uses also works, with the image on the line after the params:

Studio's hotspot starter uses this form with the question "Select the relevant areas
on the image." Both this heading form and the saved image form with `question:` keep
the question inside the assessment. The shared authored starter catalog is English;
authors can replace its question and image description with their course language.

```prax
### Select the release pin.
as: hotspot

/assets/diagram.png
alt: Labeled safety diagram
spot: Valve; 30%; 15%
spot: Release pin; 45%; 25%; correct
```

> **Important:** Keep every parameter, including each `spot:` line, together with no blank lines between them. Studio stops reading parameters at the first blank line, so it silently drops any `spot:` after one.

**Spot syntax:**

```prax
spot: Label; x%; y%; correct
spot: Valve; rect 10% 20% 30% 15%; correct
spot: Gauge; ellipse 60% 40% 8% 5%
spot: Panel; polygon 10% 10%, 40% 10%, 40% 30%, 10% 30%; correct
```

- `Label` is the learner-visible hotspot label.
- All coordinates and sizes are percentages of the displayed image. The `%` sign is optional.
- The point form uses `x` across and `y` down the image. It draws the default circle.
- `rect` takes left, top, width and height. `ellipse` takes centre x, centre y, radius x and radius y.
- `polygon` takes at least three comma-separated `x y` vertices.
- `correct` marks the spot as a correct selection.

Regions must use finite numbers, stay within the image and have positive dimensions. Polygons need at least three vertices and nonzero area. Studio warns and omits an invalid spot while keeping the other spots. Escape a semicolon inside a label as `\;`.

Horizontal values are percentages of the image width and vertical values are percentages of its height, so equal radii make a circle only on a square image. Studio warns when a region's bounding width or height is under 8% of the image. For an ellipse, those dimensions are twice its radii. On a wide image, check that each region is still easy to select at a 320 pixel width. Multi-select modes, custom indicators and per-spot feedback are not yet exposed as public `.prax` syntax.

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
Add `feedback: <text>` before the scale lines to show feedback after the learner selects a rating. The feedback reappears when a saved rating is restored.

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

Matrix option columns reserve room for their longest words. Labels wrap between words without automatic hyphenation. The statement column yields width first, and a narrow screen scrolls the table within the assessment rather than the page.

## categorize

Category sorting.

Category option columns reserve room for their longest words. Labels wrap between words without automatic hyphenation. The item column yields width first, and a narrow screen scrolls the table within the assessment rather than the page.

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

Serialization preserves the group title, its authored heading level, settings, authored IDs, and member
references. It writes `close: assessment-group` after the last member so the next
question remains outside the group. Nested assessment headings retain their
authored level so they remain inside their parent container.

Group multiple assessments. Put `as: assessment-group` on a `#` heading when its title is the page's primary heading. For a group nested under a page title, use `##` or a deeper heading level and follow it with appropriately nested question headings in Prax source. In learner output, the group title remains a heading and each question stem renders as a bold paragraph labelled to its response controls. Ordinary headings do not end the group. Use `close: assessment-group` before following content on the same page; a page break or the end of the file also closes it.

Content after a closed group uses the same [block spacing](frontmatter-design.md#spacing-and-layout) as content after an ordinary assessment. This applies to standard pages, module decks, and Studio previews. The group's header, questions, and submit footer keep their shared border without added gaps inside the group.

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

**`mode: oneOf`** allows assessment choice. The learner selects one question to answer. For example, in a group of 5 questions with `mode: oneOf`, the learner can choose any single question to answer.

The question chooser's radio rows also remain stationary while pressed, and
selecting their label or row padding chooses the question.

Note: `passingScore:` is the correct parameter name (not `passing:`).

**Parameters:**

| Parameter | Type | Description |
|---|---|---|
| `mode` | `all \| oneOf` | `all` is the default. The group finishes when every required member completes; when none is required, every member must complete. `oneOf` uses only the learner's selected question. |
| `passingScore` | number | Minimum score (0–100) to pass the group. |
| `showResultsSummary` | boolean | Show a results summary after the required members submit, or all members if none is required. In `oneOf`, show the selected question's result. Defaults to `true`. |
| `pointsOverride` | number | Override total point value for the group (instead of summing individual question points). |
| `requireAll` | boolean | Whether all questions must be answered before the group action runs. Defaults to `false` for every group. Set to `true` to require all answers first. |
| `buttonLabel` | text | Override the visible group action text. |
| `deckGate` | `attempt \| pass` | In Studio module decks, release the next slide after group submission or a passing group result. See [module deck settings](module-deck.md#longer-assessments-and-surveys). |

A member question can use `name:` as a logic anchor and `id:` as a durable identity. These parameters do not change its group membership or shared submit action.

The group's `width` (`narrow`, `wide`, `full`, or `breakout`) sets one width for its header, context, questions, and submit area, including members with their own width. When the group omits `width`, it uses the widest member width (`full`, then `breakout`, then `wide`); it uses `narrow` when every member is narrow; otherwise it keeps the normal content width. Older source that sets these widths with `layout:` still works; new source should use `width:`. Published pages apply the resolved width before interaction initializes, so activation preserves question widths.

Group progress counts the same committed question results that mark each member complete. After the required
members have submitted, the localized result is announced. The group action remains
available while any member is unanswered or can retry, even after the group finishes.
It is removed once every member finishes. In
`mode: all`, ungraded groups default to **Check all** and graded groups to **Submit all**.
In `mode: oneOf`, only the selected member is shown, submitted, and counted toward the result;
the defaults are **Check selected** and **Submit selected**.

In a graded `mode: all` group, the score averages answered graded questions and unanswered
required graded questions. An unanswered required question counts as zero. An unanswered
optional question does not affect the score. When every question is required, the score
still averages every graded question. The group action leaves unanswered optional questions
untouched so the learner can submit them later.
If no question is required, the group waits for every answer before reporting a score.

The "Questions completed" counter appears when a group contains a scored or automatically checked question. A choice question with no correct option is a survey, so it does not show the counter on its own. A group containing only open responses has no counter. In an ungraded `mode: all` group, the learner can use the group action with some answers still empty. Answered items save separately and return on resume, including from SCORM suspend data. The group finishes when every required member is complete. If no member is required, all members must complete as before. Set `requireAll: true` to require all answers before the action runs.

When at least two text responses in any group set `downloadAs`, the learner sees one group download control. It offers the configured TXT and DOCX formats and puts each full downloadable prompt before its answer in authored order. The individual download controls are hidden. A group with one downloadable response keeps that response's controls. Graded groups still show the completion counter. Downloads stay available after the group is complete.

Published assessments and learner activities use one neutral surface with a 1px semantic border.
This activity material is distinct from tinted callouts and unfilled quotes and applies to
standalone questions, grouped assessments, checklists, ratings, and signatures.

## Shared scoring parameters

Ratings (`as: rating`) use only `required` from this table. They also accept `feedback:` as described above.

| Parameter | Type | Description |
|---|---|---|
| `points` | number | Point value awarded for a correct answer. Used in scored assessment groups and SCORM/xAPI reporting. |
| `required` | boolean | Whether the learner must answer this question before proceeding. |
| `timed` | number | Intended time limit in seconds. **Not applied yet:** Studio shows no countdown. |
| `attempts` | number | Maximum number of attempts before the answer is locked. Use `0` for unlimited attempts. |
| `shuffle` | boolean | Randomize option/item order on each attempt. Available on: choose-one, choose-many, match, order, categorize. |

Submitting auto-scored work always shows and announces a localized **Correct** or **Incorrect**
status, even when no feedback was authored. Authored feedback is optional and appears after that
status. Completion-only work announces **Completed** instead of a score or correctness result.

## Decorator parameters

Decorator parameters add metadata for learning analytics and adaptive behavior:

| Parameter | Type | Description |
|---|---|---|
| `scored` | boolean | Whether this assessment contributes to the overall course score. Defaults to `false`, including inside an assessment-group. Set `true` on each scored member. |
| `competency` | text | Competency tag or identifier this question maps to (e.g. `"fire-safety"`). **Not applied yet:** Studio does not send it in xAPI statements. |
| `confidence` | boolean | Intended to enable confidence-based marking. **Not applied yet:** Studio shows no confidence prompt. |
| `retrieval` | boolean | Marks this as a retrieval practice question. **Not applied yet:** it does not affect analytics. |
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
- fill-blank: `inline-inputs`, `dropdown`, and `word-bank` implemented. `bank:` supplies the shared word bank.
- assessment-group mode: `all | oneOf`.
