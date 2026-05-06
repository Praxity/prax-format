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

## Not yet in the grammar

The following block types are **not recognized by the parser** — they either fall through to unknown/plain rendering or require backend infrastructure not available in standalone exports:

- **Interactive video** — multi-clip video sequences with timed interactions (Mux player). Created through the editor's video timeline interface, not via `.prax` grammar syntax.
- **Discussion / Q&A** (`as: discuss`, `as: qa`) — threaded discussion requiring a live backend. Grammar syntax exists but rendering requires Praxity Cloud publish.
- **Contribution / Poll** (`as: contribute`, `as: poll`) — shared responses requiring a live backend. Grammar syntax exists but rendering requires Praxity Cloud publish.
- **Branch** (`as: branch`) — branching scenario. `as: branch` is **not recognized by the parser** — it falls through to an unknown `as:` value and renders as a plain heading. Do not use `as: branch` in authored `.prax` files. Use conditional visibility (`visible:` with variables and `when:` rules) for branching logic.
- **Document / file download** (`as: document`) — file download block. Not yet in the grammar; use a button (`[label](url)` + `as: button`) as an alternative.
- **File upload** (`as: file-upload`) — assessment file submission block. Not yet in the grammar.
