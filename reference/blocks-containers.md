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

Card container for grouped rich content. Items are `###` headings, rendered in a grid layout with configurable columns. Requires explicit `close: card`.

**Syntax:**
```prax
### Fire Safety
as: card
columns: 2

Know your nearest exit and extinguisher location.

### Chemical Safety

Always check the SDS before handling chemicals.

### Electrical Safety

Lock out/tag out before any maintenance work.

close: card
```

**Parameters:**
- `columns` (number) — number of grid columns.
- `layout`: `wide | full | breakout`

Cards with `flip: true` create front/back flashcard behavior — see [blocks-interactive.md — flashcard](blocks-interactive.md#flashcard).

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
| `card` | `close: card` recommended | Explicit close prevents ambiguity |
| `sequence` | Implicit | Closed by next heading at same or higher level, or page break |
| `comparison` | Implicit | Closed after second item |
