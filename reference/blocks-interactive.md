# Interactive Blocks

Decorative motion does not control an interaction's availability or completion.
The next available label in a sequential labeled graphic receives an opacity and
scale ring for three 1.5-second cycles. The ring then disappears; the label stays
operable. Opening it makes the next label available with its own bounded cue.
Reduced-motion preferences suppress the cue. This runtime policy also applies to
older persisted labeled-graphic data and adds no authored parameter.

## checklist

Task list transformed into an interactive checklist. Write the task items first, then add `as: checklist` and any parameters after the list.

**Syntax:**
```prax
- [ ] Inspect emergency exits
  Check that each route stays clear.
- [ ] Confirm PPE availability
- [ ] Verify first aid kit
as: checklist
required: true
```

Indent continuation text by at least two spaces. Blank lines followed by indented
text also continue the same checklist item. Studio joins that text into the
item's label. Recognized parameter lines stay outside the item.

**Parameters:**
- `required` (boolean), whether all items must be checked to proceed.
- `shuffle` (boolean), randomize item order.
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
- `label` (string), overrides the label supplied by the heading, displayed above the input methods.
- `sublabel` (string), supporting text below the unsigned input.
- `required` (boolean), require a signature before proceeding. Default: `false`.
- `mode`: `draw | type`, initially selected input method (default: `draw`). Both methods remain available, including keyboard-accessible typing.
- `width`: `narrow | wide | full | breakout`

After signing, both method tabs expose an unavailable state. One tab remains in
the Tab order; Arrow Left, Arrow Right, Home and End let learners inspect the
locked methods without changing the signature or its displayed panel. The visible
instruction explains that clearing the signature unlocks the methods. Clear
returns focus to the current input and restores method selection. Typed signatures
restored from learner state keep the same lock and recovery behaviour.
