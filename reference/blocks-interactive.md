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
