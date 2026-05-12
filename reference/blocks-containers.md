# Container Blocks

## accordion

Collapsible panels. The heading with `as: accordion` opens the accordion; subsequent headings at the same level become additional panels. Any heading level works (`##`, `###`, `####`), though `###` is most common.

**Syntax:**
```prax
### Hazard Types
as: accordion
style: contained

Chemical, electrical, and mechanical hazards.

### Control Measures

Engineering controls, administrative controls, and PPE.

### Reporting

File an incident report within 24 hours.
```

**Parameters:**
- `style`: `default | contained | separated` — visual style of accordion panels.
- `allowMultipleOpen` (boolean) — whether multiple panels can be open at once.
- `layout`: `wide | full | breakout`

## tabs

Tabbed content panels. The heading with `as: tab` opens the tab group; subsequent headings at the same level become additional tabs. Any heading level works.

**Syntax:**
```prax
### Before Shift
as: tab

Review overnight incidents and pending actions.

### During Shift

Use the checklist and escalate unresolved hazards.
```

**Parameters:**
- `layout`: `wide | full | breakout`

## columns

Column layout. Each `as: col` starts a new column. Columns must be explicitly closed with `close: col`.

**Syntax:**
```prax
as: col

/assets/safety-diagram.png
alt: Workplace safety zones diagram

as: col

### Key Safety Zones
- Loading dock
- Chemical storage
- Assembly line

close: col
```

Any content can go inside a column — headings, images, text, lists, even nested blocks.

**Parameters:**
- `layout`: `wide | full | breakout`

## sequence

Step-by-step or timeline container. Each `###` heading becomes a labeled step or point in the sequence.

**Syntax:**
```prax
### Report the incident
as: sequence
variant: timeline
orientation: vertical

Notify your supervisor immediately.

### Secure the area

Cordon off the affected zone.

### Document findings

Take photos and complete the incident form.
```

**Variants:**
- `variant`: `numbered | timeline | plain`
- `orientation`: `vertical | horizontal`
- `alignment`: `left | center | right` — controls text alignment within steps.
- `distribution`: `uniform | scaled` — controls spacing between steps.
- `scrollable` (boolean) — whether the sequence is scrollable.

## comparison

Comparison of two items — text or images.

**Syntax (side-by-side text):**
```prax
### Before
as: comparison
style: side-by-side

Manual triage with paper forms.

### After

Automated triage with digital checklists.
```

**Syntax (image slider):**
```prax
### Before renovation
as: comparison
style: slider

/assets/before.jpg
alt: Office before renovation

### After renovation

/assets/after.jpg
alt: Office after renovation
```

The `slider` style renders an interactive drag handle that reveals the before/after images. Supports keyboard (arrow keys) and pointer drag. Includes ARIA `role="slider"`.

**Variants:**
- `style`: `side-by-side | slider`

## card

Unified card container for static cards, carousels, and flip cards. Requires explicit `close: card`.

When a card container is opened from a heading, that heading is the card group title. It is rendered around the group, not inside an individual card face.

Card items are created by headings one level below the group heading. For a `##` card group, each `###` heading starts a new card item. The item heading becomes the card label/header. It is not part of the flippable front or back face.

**Syntax:**
```prax
### Safety Concepts
as: card
layout: single
transition: slide
showProgress: true

### Hazard Identification

Recognize potential injury sources before work begins.

card: back
Start with the task, environment, tools, and materials. Document anything with harm potential.

### Risk Control

Apply controls in order: elimination, substitution, engineering controls, administrative controls, then PPE.

card: back
Use the hierarchy of controls to choose the strongest feasible mitigation.

close: card
```

**Parameters:**
- `layout`: `single | grid | masonry` — card presentation mode.
- `columns` (number) — number of columns when `layout` is `grid` or `masonry`.
- `style`: `none | outline | filled` — card item chrome treatment.
- `shadow`: `theme | none | subtle | elevated` — card depth treatment.
- `advance` (number, seconds) — auto-advance interval for `layout: single`; `0` = manual.
- `transition`: `none | fade | slide | zoom` — transition style for `layout: single`.
- `showProgress` (boolean) — show pagination/progress controls in `layout: single`.
- `shuffle` (boolean) — randomize card item order.
- `trackCompletion` (boolean) — track learner interaction/completion at card level.

Use `card: back` to mark the back face of an item. Content before `card: back` is the front face.

Do not put the next item heading immediately after `card: back` if you intend that heading to appear on the back face. A same-level item heading starts the next card. Use paragraph text or a lower-level heading for back-face content.

`as: flashcard` is a deprecated parser alias for `as: card` and should not be used for new content.

**Flip-card syntax (`card: back`):**
```prax
## Safety Terms
as: card
layout: single
style: outline

### What is lockout/tagout?
Energy isolation before maintenance on energized equipment.

card: back
#### Answer
A formal energy-isolation procedure required before servicing machinery.

### When is a confined space permit required?

Before entering any space with limited entry/exit and potential hazardous atmosphere.

card: back
#### Answer
Permits are required when atmospheric or entrapment risks are present.

close: card
```

**Headingless single-card syntax:**
```prax
as: card
layout: single
style: outline

/assets/control-panel.png
alt: Control panel with emergency stop highlighted

card: back
This panel includes the emergency stop, lockout point, and status indicator.

close: card
```

Headingless `as: card` is useful for a single card whose front face is image or paragraph content. Multiple cards in one group currently need item headings; another standalone `as: card` inside an open card creates a nested card, not a sibling card item.

## Nesting rules

- Containers may include content blocks and assessments as children.
- Avoid deeply nested multi-container chains for readability.
- Page breaks (`---`) close all open containers automatically.

## Closing rules

| Container | Closing | Notes |
|---|---|---|
| `accordion` / `tabs` | Implicit | Closed by next heading at same or higher level, or page break |
| `col` | `close: col` required | Must explicitly close the column layout |
| `assessment-group` | `close: assessment-group` required | Must explicitly close |
| `card` | `close: card` required | Must explicitly close; applies to all card layouts |
| `sequence` | Implicit | Closed by next heading at same or higher level, or page break |
| `comparison` | Implicit | Closed after second item |
