# Container Blocks

Containers use the normal content width by default. Set `layout: wide|full|breakout`
to widen a container. Cards use `layoutMode:` for width when `layout:` selects
`grid`, `masonry`, or `single`. Sequences do not widen automatically as items are added.

## accordion

Collapsible panels. The heading with `as: accordion` opens the accordion; subsequent headings at the same level become additional panels. Any heading level works (`##`, `###`, `####`), though `###` is most common.

**Syntax:**
```prax
### Hazard Types
as: accordion
style: shaded

Chemical, electrical, and mechanical hazards.

### Control Measures

Engineering controls, administrative controls, and PPE.

### Reporting

File an incident report within 24 hours.
```

**Parameters:**
- `style`: `none | outline | shaded | primary | secondary` — surface treatment. `none` removes fill, border and separators; `outline` adds an unshaded border; `shaded` uses the neutral surface; `primary` and `secondary` use their brand tints. Omitted style retains the original separator-line treatment. Legacy `default`, `contained` and `separated` source remains supported with its original appearance.
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

The tab strip and active panel share a neutral surface and continuous hairline boundary, so the
selected tab remains visibly attached to its content without relying on color. The first tab label
starts on the page content spine. Published tabs use manual activation: arrow keys move focus,
and Enter or Space selects the focused tab.

Set `style: default|outline|pills` and `orientation: horizontal|vertical` on
the first `as: tab` item to style the whole tab group. Defaults are `default` and
`horizontal`. Both settings survive serialization.

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

Step-by-step or timeline container. The opening heading sets the item level; subsequent headings at that level become labeled steps.

**Syntax:**
```prax
### Report the incident
as: sequence
style: timeline
orientation: vertical

Notify your supervisor immediately.

### Secure the area

Cordon off the affected zone.

### Document findings

Take photos and complete the incident form.
```

**Variants:**
- `style`: `numbered | timeline | none`
- `orientation`: `vertical | horizontal`
- `alignment`: `left | center | right` — controls text alignment within steps.
- `distribution`: `uniform | scaled` — controls spacing between steps.
- `scrollable` (boolean) — whether the sequence is scrollable.

## comparison

Comparison of two items — text or images.

Each item owns the blocks between its heading and the next item heading.
Descriptions and media stay inside their respective columns. `close: comparison`
ends the comparison before following prose. Serialization preserves these children
and writes an explicit closing marker for heading-based containers.

**Syntax (side-by-side text):**
```prax
### Before
as: comparison
style: side-by-side

Manual triage with paper forms.

### After

Automated triage with digital checklists.
```

The two authored headings label the columns. Preset labels such as “Option A” / “Option B”
or “Before” / “After” are used only when a stored column has no authored heading; authored
headings always win while keeping the preset accent rule.

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

Image sliders are authored with `as: comparison` and `style: slider`. There is no separate public `as: imageComparison` block.

The `slider` style renders an interactive drag handle that reveals the before/after images. Supports keyboard (arrow keys) and pointer drag. Includes ARIA `role="slider"`.

**Variants:**
- `style`: `side-by-side | slider`

## card

Unified card container for static cards, carousels, and flip cards. Requires explicit `close: card`.

When a card container is opened from a heading, that heading is the card group title. It is rendered around the group, not inside an individual card face.

Card items are created by headings one level below the group heading. For a `##` card group, each `###` heading starts a new card item. The item heading becomes the card label/header. It is not part of the flippable front or back face.

**Syntax:**
```prax
## Safety Concepts
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
- `headingLevel` (`2` to `6`). Sets the heading level for item labels in a standalone `as: card` group. The default is `3`.
- `headings` (boolean). Controls whether item labels participate in heading navigation. The default is `true`; use `false` for presentation-only or storytelling cards.
- `style`: `none | outline | shaded | primary | secondary` — surface treatment across grid, masonry, single-card decks and flip faces. `none` removes surface chrome; `outline` is transparent with a border; `shaded` uses the neutral surface; `primary` and `secondary` use their brand tints. Legacy `filled` aliases `shaded`; `accent` aliases `primary`. Old `filled`/`shaded`/`accent` plus `color: primary` or `color: secondary` is accepted and serialized as the corresponding named style.
- `shadow`: `theme | none | subtle | elevated` — card depth treatment.
- `advance` (number, seconds) — auto-advance interval for `layout: single`; `0` = manual.
- `transition`: `none | fade | slide | zoom` — transition style for `layout: single`.
- `showProgress` (boolean) — show pagination/progress controls in `layout: single`.
- `shuffle` (boolean) — randomize card item order.
- `trackCompletion` (boolean) — track learner interaction/completion for `layout: single` only.

Use `card: back` to mark the back face of an item. Content before `card: back` is the front face.

Card item headings keep the level authored in `.prax`. For example, a `##` card group with `###` items renders those labels as `<h3>`. To make standalone card items page-level sections, set `headingLevel: 2` and author each item with `##`. Use `headings: false` when labels should remain visual labels rather than document headings.

```prax
as: card
headingLevel: 2
style: outline

## Who has been left behind?
Identify the groups facing the greatest need.

## Which standards are at stake?
Identify the affected housing standards.

close: card
```

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
| `accordion` / `tab` | Optional explicit closer | `close: accordion` / `close: tab`, a higher-level heading, a same-level heading declaring another block type, or a page break |
| `col` | `close: col` required | Must explicitly close the column layout |
| `assessment-group` | `close: assessment-group` required | Must explicitly close |
| `card` | `close: card` required | Must explicitly close; applies to all card layouts |
| `sequence` | Optional explicit closer | `close: sequence`, a higher-level heading, a same-level heading declaring another block type, or a page break |
| `comparison` | Optional explicit closer | `close: comparison` or a structural boundary; the second item does not automatically close the group |

Same-level headings without a different `as:` declaration add items to heading-based
containers. Use an explicit closer before following prose or columns that belong
outside the group. `close: tabs` remains accepted as an alias for `close: tab`.
