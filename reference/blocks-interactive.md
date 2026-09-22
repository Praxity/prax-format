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
- `width`: `narrow | wide | full | breakout`

## signature

The heading supplies the signature label. Parameters follow the heading; ordinary
prose after the parameters starts a separate text block.

Learner signoff with optional draw or type modes. The learner signs to confirm they have reviewed the content.

**Syntax:**
```prax
### I confirm I have reviewed this safety module
as: signature
mode: type
sublabel: Trainee signature
required: true
```

**Parameters:**
- `label` (string) — overrides the label supplied by the heading, displayed above the input methods.
- `sublabel` (string) — supporting text below the unsigned input.
- `required` (boolean) — require a signature before proceeding. Default: `false`.
- `mode`: `draw | type` — initially selected input method (default: `draw`). Both methods remain available, including keyboard-accessible typing.
- `width`: `narrow | wide | full | breakout`
