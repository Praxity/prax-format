# Containers — .prax Sub-Skill

## How containers work

Containers hold child blocks. In v3, containers are opened from headings using `as:` and closed by structure or explicit `close:`. Some containers are implicit (accordion, tabs, sequence) — subsequent sibling headings at the same level become additional items. Others are explicit and need `close:`.

Key rules:

- Accordion, tab, and sequence items are `###` headings.
- First heading in a group carries the `as:` value; subsequent siblings join automatically.
- Columns use standalone `as: col` sections (no heading needed).
- `close: col` is required for columns.
- `close: assessment-group` is required for assessment groups.
- `close: card` and `close: flashcard` are required when opened with standalone `as:`.
- A page break (`---`) closes all still-open containers on that page.

## disclosure (accordion / tabs)

The `disclosure` keyword covers both accordion and tab modes. The `style` axis selects accordion vs tabs. The `accordionStyle` axis selects the visual treatment of the accordion.

### Accordion

```prax
### Hazard Signals
as: accordion
accordionStyle: contained
allowMultipleOpen: false

Red indicates immediate danger. Stop all nearby work.

### Emergency Contacts

Post local contacts in every lab and near all exits.

### Incident Report

Document all near-miss events within 24 hours using form HS-12.
```

Accordion with `separated` style:

```prax
### Module Overview
as: accordion
accordionStyle: separated

Introduction to risk identification and control.

### Learning Objectives

By the end, learners can categorize hazards by type and severity.
```

### Tabs

```prax
### Before Shift
as: tab

Review incident updates and sign the pre-shift checklist.

### During Shift

Follow the inspection checklist at each station.

### After Shift

Log any observations in the safety management system.
```

### disclosure parameters

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `style` | enum | `accordion`, `tabs` | accordion | Selects accordion vs tabs mode |
| `accordionStyle` | enum | `default`, `contained`, `separated` | default | Visual treatment of accordion panels |
| `allowMultipleOpen` | boolean | true / false | false | Allow multiple panels open simultaneously |

Note: Do not write `style: accordion` to control the visual treatment — that axis selects the mode, not the look. Use `accordionStyle: contained` or `accordionStyle: separated` to change the panel appearance.

Planned style variants: `flip`, `stepped`, `plain`.

### Accordion nesting example

Assessments and content blocks can both appear inside accordion items:

```prax
### Safety Quiz Prep
as: accordion
accordionStyle: contained

Review these key definitions before taking the quiz.

> Always wear PPE in designated areas.
as: note
color: warning

### Practice Question

### Which PPE is required in chemical zones?
as: choice
scored: true

(x) Gloves, goggles, and lab coat
feedback: All three are mandatory in chemical zones.
( ) Gloves only
feedback: Goggles and lab coat are also required.
```

## columns

Columns are standalone — no heading required. Each `as: col` starts a new column. Adjacent columns merge into one `columns` block. Always close with `close: col`.

```prax
as: col
**Hazards** — Chemical, biological, physical, and ergonomic risks.

as: col
**Controls** — Engineering, administrative, and PPE-based solutions.

as: col
**Review** — Annual audits and monthly spot checks.

close: col
```

Two-column layout with headings inside:

```prax
as: col
### Risks
List top operating risks for this area.

as: col
### Controls
List mitigations for each risk identified.

close: col
```

### columns parameters

Columns have no additional parameters. The `layout` universal parameter applies:

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `layout` | enum | `wide`, `full`, `breakout` | — | Content width override |

## sequence

Sequence is heading-driven like accordion. The first heading carries the `as: sequence` declaration; subsequent sibling headings join the same sequence.

### Numbered

```prax
### 1. Identify Hazard
as: sequence
variant: numbered
orientation: vertical
alignment: left

Record the hazard location and type.

### 2. Assess Risk

Determine severity and likelihood of harm.

### 3. Apply Controls

Implement engineering or procedural safeguards.

### 4. Monitor

Verify controls remain effective over time.
```

### Timeline

```prax
### 2020 — Foundation
as: sequence
variant: timeline

Established initial safety protocols.

### 2022 — Expansion

Added chemical handling procedures.

### 2024 — Automation

Deployed IoT monitoring sensors.
```

### Plain

```prax
### Observation
as: sequence
variant: plain

Note conditions before starting work.

### Action

Apply controls and begin task.

### Verification

Confirm controls held throughout.
```

### sequence parameters

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `variant` | enum | `numbered`, `timeline`, `plain` | numbered | Visual style of the sequence |
| `orientation` | enum | `vertical`, `horizontal` | vertical | Layout direction |
| `alignment` | enum | `left`, `center`, `right` | left | Text alignment within items |

Planned variant values: `scroll-journey`, `carousel`, `stepper`, `hero`.

## comparison

Comparison uses a heading + `as: comparison`. The second heading in the pair joins automatically.

```prax
### Manual Process
as: comparison
style: side-by-side

Paper forms with 48-hour reporting delay.

### Digital Process

Live alerts and same-day resolution.
```

### comparison parameters

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `style` | enum | `side-by-side` | side-by-side | Layout mode |

Planned style variants: `slider`, `toggle`, `diff`.

## card

Card can be opened from a heading or from a standalone `as: card`. Standalone cards require `close: card`.

```prax
### Incident Simulation
as: card

Read the scenario details below.

A worker notices a leaking chemical drum near the loading bay. No one else is present.

close: card
```

### card parameters

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `layout` | enum | `wide`, `full`, `breakout` | — | Content width override |
| `flip` | boolean | true / false | false | Enables flip-card behavior |

## flashcard

Flashcard items are heading-driven. A group of flashcards shares the `as: flashcard` declaration on the first heading. Standalone flashcard blocks require `close: flashcard`.

```prax
### What is lockout/tagout?
as: flashcard

Energy isolation procedure required before servicing equipment.

### What is a near miss?

An unplanned event that did not result in injury but had the potential to.

close: flashcard
```

### flashcard parameters

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `layout` | enum | `single`, `grid`, `masonry` | — | Card display layout |
| `flip` | boolean | true / false | false | Enables flip-card interaction |

## Nesting rules

Safe nesting patterns:

- Any content block (text, image, note, quote, video, button) inside accordion, tab, or sequence items.
- Assessments inside accordion or sequence items.
- Columns inside normal page flow.
- Flashcards can hold content blocks inside each item.

Avoid nesting containers inside containers (accordion inside accordion) unless required for pedagogy.

## Closing rules

Summary of which containers require explicit `close:` and which are closed by structure:

| Container | Close required | Closed by |
|---|---|---|
| accordion | No | Next `##` heading or page break |
| tab | No | Next `##` heading or page break |
| sequence | No | Next `##` heading or page break |
| comparison | No | Next `##` heading or page break |
| columns | Yes | `close: col` |
| card (standalone) | Yes | `close: card` |
| flashcard (standalone) | Yes | `close: flashcard` |
| assessment-group | Yes | `close: assessment-group` |

Page breaks (`---`) close all open containers on the current page regardless of type.
