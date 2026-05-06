# Container Blocks

## disclosure

Disclosure provides accordion/tabs grouping. Both `as: accordion` and `as: tab` produce a `disclosure` block — this is the underlying manifest keyword. The `as:` alias determines which `style` axis value is set internally.

**Syntax (accordion):**
```prax
### Topic A
as: accordion
accordionStyle: contained
allowMultipleOpen: false

Topic A details.

### Topic B

Topic B details.
```

> **`accordionStyle` controls the visual variant**, not `style`. Use `accordionStyle: contained | separated | default`. The `style` axis selects accordion vs. tabs mode and is set implicitly by the `as:` value — do not set `style: accordion` manually.

**Tabs variant:**

```prax
### Step 1
as: tab

Explain step 1.

### Step 2

Explain step 2.
```

**Variants:**
- `style`: `accordion | tabs` (set implicitly by `as: accordion` or `as: tab`)
- `accordionStyle`: `default | contained | separated`
- `allowMultipleOpen`: boolean

## columns

Column layout via repeated standalone `as: col`.

**Syntax:**
```prax
as: col
### Left Column
Left content.

as: col
### Right Column
Right content.

close: col
```

**Parameters:**
- `layout`

## sequence

Step-by-step or timeline container.

**Syntax:**
```prax
### Step 1
as: sequence
variant: numbered
orientation: vertical
alignment: left

Describe step 1.

### Step 2

Describe step 2.
```

**Variants:**
- `variant`: `numbered | timeline | plain` (plus planned values)
- `orientation`: `vertical | horizontal`
- `alignment`: `left | center | right`

## comparison

Before/after or side-by-side comparison.

**Syntax:**
```prax
### Before
as: comparison
style: side-by-side

Manual triage.

### After

Automated triage.
```

**Variants:**
- `style`: `side-by-side` (implemented), `slider|toggle|diff` planned

## card

Card container for grouped rich content. `card` is parser-supported as a container frame but is not listed in the manifest's `INTERACTIVE_KEYWORDS` or `ASSESSMENT_KEYWORDS`. It works as a standalone block but does not appear in the block picker.

**Syntax:**
```prax
### Decision Card
as: card

Scenario details and options.

close: card
```

**Layout variants (card):**

| Variant | Values | Notes |
|---|---|---|
| `layout` | `single \| grid \| masonry` | Controls card arrangement when multiple cards are used |
| `flip` | boolean | Set `flip: true` for flashcard-style front/back behavior |

## Nesting rules

- Containers may include content and assessments.
- Avoid deeply nested multi-container chains for readability.
- Page breaks close any open container state.

## Closing rules

- Required: `close: col`, `close: assessment-group`.
- Recommended when standalone: `close: card`, `close: flashcard`.
