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

Card container for grouped content, flip cards, and slide-like card flows. Items are `###` headings and the container requires explicit `close: card`.

`as: flashcard` is accepted as an alias for `as: card` but is not recommended for new content.

**Static card grid (no back side):**
```prax
### Safety Categories
as: card
layout: grid
columns: 3
style: outline
shadow: subtle

### Chemical
Label, store, and segregate chemicals by compatibility.

### Electrical
De-energize equipment and verify zero-energy state before service.

### Ergonomic
Adjust workstation height and posture to reduce strain.

close: card
```

**Flip card carousel (front is implicit, `card: back` defines back side):**
```prax
### Hazard Drill
as: card
layout: single
transition: slide
advance: 0
showProgress: true
trackCompletion: true

### What is lockout/tagout?
Energy isolation before maintenance on energized equipment.

card: back
Lockout/tagout prevents accidental startup and must be verified before work begins.

### When is a confined space permit required?
Before entering any space with limited entry/exit and potential hazardous atmosphere.

card: back
A permit is required when atmospheric, engulfment, or mechanical hazards are present.

close: card
```

**Parameters:**
| Param | Values | Default |
|---|---|---|
| `layout` | `single` / `grid` / `masonry` | `grid` |
| `columns` | number | `1` |
| `style` | `none` / `outline` / `filled` | `none` |
| `shadow` | `theme` / `none` / `subtle` / `elevated` | `theme` |
| `advance` | number (seconds, `0` = manual) | `0` |
| `transition` | `none` / `fade` / `slide` / `zoom` | `fade` |
| `showProgress` | boolean | `true` |
| `shuffle` | boolean | `false` |
| `trackCompletion` | boolean | `false` |

## Nesting rules

- Containers may include content blocks and assessments as children.
- Avoid deeply nested multi-container chains for readability.
- Page breaks (`---`) close all open containers automatically.

## Closing rules

| Container | Closing | Notes |
|---|---|---|
| `accordion` | `close: accordion` optional | Implicit closing by next heading at same or higher level, or page break still works |
| `tabs` | `close: tabs` optional | Implicit closing by next heading at same or higher level, or page break still works |
| `sequence` | `close: sequence` optional | Implicit closing by next heading at same or higher level, or page break still works |
| `comparison` | `close: comparison` optional | Implicit closing still works |
| `col` | `close: col` required | Must explicitly close the column layout |
| `assessment-group` | `close: assessment-group` required | Must explicitly close |
| `card` | `close: card` required | Must explicitly close |
