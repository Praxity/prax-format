# Interactive Blocks

## checklist

Task list transformed into checklist.

**Syntax:**
```prax
- [ ] Inspect exits
- [ ] Confirm PPE
- [ ] Verify first aid kit
as: checklist
required: true
shuffle: false
```

## signature

Learner signoff.

**Syntax:**
```prax
### I confirm I reviewed this module
as: signature
mode: draw
```

Modes from manifest: `draw`, `type`.

## discussion

Discussion or Q&A prompt. Both `as: discuss` and `as: qa` produce a `discussion` block (the manifest keyword). The `as:` alias sets the `mode` variant internally: `discuss` → `mode: discuss`, `qa` → `mode: qa`.

**Syntax:**
```prax
### Share one challenge from your context
as: discuss

Include one workaround you tested.
```

Q&A mode:

```prax
### Ask your question
as: qa
```

## contribution

Shared response or poll. Both `as: contribute` and `as: poll` produce a `contribution` block (the manifest keyword). The `as:` alias sets the `mode` variant internally: `contribute` → `mode: share`, `poll` → `mode: poll`.

**Syntax:**
```prax
### Share a best practice from your team
as: contribute
```

Poll mode:

```prax
### What is the top risk in your environment?
as: poll
```

## ai-chat

Guided AI chat helper.

**Syntax:**
```prax
### Ask about hazard controls
as: ai-chat
topic: Hazard Controls
prompt: You are a safety coach. Explain controls with concise examples.
```

## branch

Branching scenario block (manifest keyword present; rendering support may vary by runtime build).

**Syntax (authoring pattern):**
```prax
### Incident Branching Scenario
as: branch
style: scenario-cards

Choose the action that best reduces immediate risk.
```

## flashcard

Flashcard interaction style. `flashcard` is a **container-type** — it is parser-supported as a `ContainerFrame` (type `"flashcard"`) but is not listed in `INTERACTIVE_KEYWORDS` in the manifest. It does not appear in the block picker and has no manifest entry.

**Syntax:**
```prax
### What is lockout/tagout?
as: flashcard

Energy isolation before maintenance.

### When is lockout required?

Before any maintenance on energized equipment.
close: flashcard
```

## Parameter summary

- `checklist`: `required`, `shuffle`, `layout`
- `signature`: `label` (manifest required), `mode`, `layout`
- `discussion`, `contribution`: `layout`
- `ai-chat`: `prompt`, `topic`, `layout`
- `branch`: style variants in manifest (`scenario-cards` implemented baseline)
