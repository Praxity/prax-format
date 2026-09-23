# Container Blocks

Containers use the normal content width by default. Set `width: narrow|wide|full|breakout`
to change a container's width. Cards independently use `layout:` to select
`grid`, `masonry`, `slides`, or `rows`. Sequences do not widen automatically as items are added.

A blank line before `as: card`, its legacy alias `as: flashcard`, or `as: col`
starts a standalone container and leaves the preceding heading or content outside.
To attach a card to a heading, put `as: card` directly beneath that heading.
Accordion, tab, sequence, comparison, and assessment-group declarations require a
heading; their existing heading attachment also accepts intervening blank lines.

For compatibility when reading source with inline narration overrides, `narration`, `narrationVoice`,
`narrationLanguage`, `narrationSpeed`, `narrationDisabled`, and recording metadata on
an item heading belong to that item. This applies to every accordion, tab, and sequence
heading, including the first, and to card item headings. Overrides do not carry over to
the next item. A card's outer title retains its own narration; a lower-level heading
inside a front or back face remains a separate narrated content block. Existing
whole-card narration retains its whole-container behavior. Studio saves narration in
`narration.yaml`; serializing blocks to `.prax` does not emit in-memory narration fields.

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
- `width`: `narrow | wide | full | breakout`

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
- `width`: `narrow | wide | full | breakout`

The tab strip and active panel share a neutral surface and continuous hairline boundary, so the
selected tab remains visibly attached to its content without relying on color. The first tab label
starts on the page content spine. Published tabs use manual activation: arrow keys move focus,
and Enter or Space selects the focused tab.

Set `style: default|outline|pills` and `orientation: horizontal|vertical` on
the first `as: tab` item to style the whole tab group. Defaults are `default` and
`horizontal`. Both settings survive serialization.

## columns

Column layout. Each `as: col` starts a new column. Use `close: col` to end the column layout before following content on the same page.

**Syntax:**
```prax
as: col
weight: 2

/assets/safety-diagram.png
alt: Workplace safety zones diagram

as: col
weight: 1

### Key Safety Zones
- Loading dock
- Chemical storage
- Assembly line

close: col
```

Any content can go inside a column — headings, images, text, lists, even nested blocks.

**Parameters:**
- `width`: `narrow | wide | full | breakout`

Set the container’s `width` on the first `as: col`. Each `as: col` accepts `weight: <positive number>` as a relative proportion. For example, weights `2` and `1` allocate two thirds and one third of the available width after the gap. Omitted or invalid weights use `1`. Default columns and side-by-side comparisons share width equally. Columns stack in source order on narrow screens. Existing fractional proportions remain valid.

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

Bulleted and numbered lists inside steps keep their text aligned to the start of
the reading direction, with hanging indents for wrapped lines. Step headings and
paragraphs retain the chosen alignment.

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
layout: side-by-side

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
layout: slider

/assets/before.jpg
alt: Office before renovation

### After renovation

/assets/after.jpg
alt: Office after renovation
```

Image sliders are authored with `as: comparison` and `layout: slider`. There is no separate public `as: imageComparison` block.

The `slider` layout renders an interactive drag handle that reveals the before/after images. Supports keyboard (arrow keys) and pointer drag. Includes ARIA `role="slider"`.

**Variants:**
- `layout`: `side-by-side | slider`

## card

Unified card container for static cards, carousels, and flip cards. Use `close: card` to mark the end of the group explicitly.

When a card container is opened from a heading, that heading is the card group title. It is rendered around the group, not inside an individual card face.

Card items are created by headings one level below the group heading. For a `##` card group, each `###` heading starts a new card item. The item heading becomes the card label/header. It is not part of the flippable front or back face.

**Syntax:**
```prax
## Safety Concepts
as: card
layout: slides
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
- `layout`: `grid | masonry | slides | rows` — card presentation mode.
- `width`: `narrow | wide | full | breakout` — container width, independent of card presentation.
- `media`: `inset | flush` — move the first front child before the item title when it is an image. Omit to keep existing child placement. `inset` pads the image; `flush` reaches the card edges. Captions travel with the image. Works in each layout and on flip-card fronts; back content is unchanged.
- `columns` (number) — number of columns when `layout` is `grid` or `masonry`.
- `headingLevel` (`2` to `6`). Sets the heading level for item labels in a standalone `as: card` group. The default is `3`.
- `headings` (boolean). Controls whether item labels participate in heading navigation. The default is `true`; use `false` for presentation-only or storytelling cards.
- `style`: `none | outline | shaded | primary | secondary` — surface treatment across grid, masonry, single-card decks and flip faces. `none` removes surface chrome; `outline` is transparent with a border; `shaded` uses the neutral surface; `primary` and `secondary` use their brand tints. Legacy `filled` aliases `shaded`; `accent` aliases `primary`. Old `filled`/`shaded`/`accent` plus `color: primary` or `color: secondary` is accepted and serialized as the corresponding named style.
- `shadow`: `theme | none | subtle | elevated` — card depth treatment.
- `advance` (number, seconds) — auto-advance interval for `layout: slides`; `0` = manual.
- `transition`: `none | fade | slide | zoom` — transition style for `layout: slides`.
- `showProgress` (boolean) — show pagination/progress controls in `layout: slides`.
- `shuffle` (boolean) — randomize card item order.
- `trackCompletion` (boolean) — track learner interaction/completion for `layout: slides` only.

Use `card: back` to mark the back face of an item. Content before `card: back` is the front face.

Generated narration reads each card front and then its back as separate clips. With Follow
narration enabled, playback and seeking reveal the exact card face, including in shuffled
layouts and without sentence timings. Narration follows authored item order. Single-card
layouts advance to the next narrated item at its clip boundary. Manual card navigation or
flipping pauses narration without changing the Follow preference. Play resumes at the current
audio position and restores the narrated face when Follow is enabled. Narration does not move
keyboard focus. Starting a module-deck slide from its beginning resets its cards before the
introduction plays, without clearing completion progress. Resuming partway through a clip does
not rewind the cards.
Card auto-advance timers wait while their container is being narrated. Manual card browsing
stops the authored timer for that mounted session; narration can still advance cards. Automatic reveals do
not count as learner flips or grant completion. Older custom aggregate scripts remain intact
and do not acquire invented front/back boundaries.

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
layout: slides
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
layout: slides
style: outline

/assets/control-panel.png
alt: Control panel with emergency stop highlighted

card: back
This panel includes the emergency stop, lockout point, and status indicator.

close: card
```

Headingless `as: card` is useful for a single card whose front face is image or paragraph content. Multiple cards in one group currently need item headings; another standalone `as: card` inside an open card creates a nested card, not a sibling card item.

### Card rows and image framing

Use `layout: rows` for unordered rich chunks with labels beside their bodies. Rows retain the item heading levels and source order, use a subtle separator, and stack the label above the body when their container is narrow. `columns` does not affect rows. Cards with back content retain their flip interaction as full-width rows.

```prax
as: card
layout: rows
style: none

### Host
Check the room booking before you send the invite.

### Guest
Use the latest invite to find the time and room.

close: card
```

For image-led cards, set `media: flush` or `media: inset` on the card and author the image as the first child of each front. Use image `ratio` and `fit` only when a shared frame is useful. `fit: contain` keeps the whole image visible; `fit: cover` explicitly crops it. Other children keep their order. If the first child is not an image, media placement has no effect.

## Nesting rules

- Containers may include content blocks and assessments as children.
- Avoid deeply nested multi-container chains for readability.
- Page breaks (`---`) and the end of the file close all open containers automatically.

## Closing rules

All containers accept an explicit closer. Page breaks and the end of
file also close every open container; an omitted closer is not a syntax error.
Use explicit closers when following content should sit outside a container on the
same page. Additional boundaries depend on the container:

| Container | Explicit closer | Other boundaries on the same page |
|---|---|---|
| `accordion` / `tab` | `close: accordion` / `close: tab` | A higher-level heading, or a same-level heading declaring another block type |
| `col` | `close: col` | The next `as: col` starts a sibling column; ordinary headings remain inside the column |
| `assessment-group` | `close: assessment-group` | Ordinary headings do not end the group |
| `card` | `close: card` | A heading above the card item level, or an item-level heading declaring another block type |
| `sequence` | `close: sequence` | A higher-level heading, or a same-level heading declaring another block type |
| `comparison` | `close: comparison` | A higher-level heading, or a same-level heading declaring another block type; the second item does not automatically close the group |

Same-level headings without a different `as:` declaration add items to heading-based
containers. Use an explicit closer before following prose or columns that belong
outside the group. `close: tabs` remains accepted as an alias for `close: tab`.
