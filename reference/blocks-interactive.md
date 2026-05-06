# Interactive Blocks

## checklist

Task list transformed into an interactive checklist. Write the task items first, then add `as: checklist` and any parameters after the list.

**Syntax:**
```prax
- [ ] Inspect emergency exits
- [ ] Confirm PPE availability
- [ ] Verify first aid kit
as: checklist
required: true
```

**Parameters:**
- `required` (boolean) — whether all items must be checked to proceed.
- `shuffle` (boolean) — randomize item order.
- `layout`: `wide | full | breakout`

## signature

Learner signoff with optional draw or type modes. The learner signs to confirm they have reviewed the content.

**Syntax:**
```prax
### I confirm I have reviewed this safety module
as: signature
mode: type
label: Trainee Signature
```

**Parameters:**
- `label` (string, required) — text displayed below the signature line.
- `mode`: `draw | type` — input mode. Note: `draw` mode alone is not accessible to all users; prefer `type` or allow both when accessibility is a concern.
- `layout`: `wide | full | breakout`

## ai-chat

Guided AI chat helper scoped to a topic.

**Syntax:**
```prax
### Ask about hazard controls
as: ai-chat
topic: Hazard Controls
prompt: You are a safety coach. Explain controls with concise examples.
```

**Parameters:**
- `topic` (string) — topic label shown to the learner.
- `prompt` (string) — system prompt for the AI assistant.
- `layout`: `wide | full | breakout`

## flashcard

Flashcard interaction — a card container with front/back flip behavior. Each `###` heading is a card front; the content below is the back. Requires explicit `close: flashcard`.

This is a variant of the [card container](blocks-containers.md#card) with `flip: true` behavior.

**Syntax:**
```prax
### What is lockout/tagout?
as: flashcard

Energy isolation before maintenance on energized equipment.

### When is a confined space permit required?

Before entering any space with limited entry/exit and potential hazardous atmosphere.

close: flashcard
```

**Parameters:**
- `layout`: `wide | full | breakout`

## Not yet in the grammar

The following block types exist in Praxity's renderer but are **not authored via `.prax` grammar syntax** — they are created through the editor UI or API:

- **Interactive video** — multi-clip video sequences with timed interactions (Mux player). Created through the editor's video timeline interface.
- **Discussion / Q&A** (`as: discuss`, `as: qa`) — threaded discussion requiring a live backend. Grammar syntax exists but rendering requires Praxity Cloud publish.
- **Contribution / Poll** (`as: contribute`, `as: poll`) — shared responses requiring a live backend. Grammar syntax exists but rendering requires Praxity Cloud publish.
- **Branch** (`as: branch`) — branching scenario. Grammar keyword is defined but the renderer is not yet implemented. Use conditional visibility (`visible:` with variables and `when:` rules) for branching logic in the current version.
