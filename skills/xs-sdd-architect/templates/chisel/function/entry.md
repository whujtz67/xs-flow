# Function Spec Templates

<!--
This document defines the minimal demo workflow for selecting a Function Spec Template.

When the Architect AI encounters a Function inside a Module, Bundle, Object, or Trait, it should first classify the Function by its primary purpose, then load the corresponding template and fill all required items plus any relevant optional items.

Keep Functions ordered according to the containing code structure or the user-specified order. Do not regroup Functions globally by category in the final spec unless the parent template explicitly asks for that.
-->

## Predicate Function

Use this category when the Function's primary role is to return one `Bool` decision that answers whether a hardware condition holds.

Typical signs:

- The return type is `Bool`.
- The Function name reads like a condition or permission check, such as `conflict`, `canAccept`, `needFlush`, or `isHit`.
- The Function combines comparisons, valid bits, state fields, reductions, or other Predicate Function results into one true/false result.
- The Function result controls a `when`, `elsewhen`, mux selection, ready/valid decision, stall, bypass, flush, or similar control behavior.

Template path:

`templates/chisel/function/predicate-func.md`

Selection rule:

Choose this template when the Function is mainly a Boolean decision, even if the Boolean expression is complex or internally uses temporary values.

## Extract Method

Use this category when the Function's primary role is to name and isolate a coherent block of logic that would otherwise be long, repeated, or require explanatory comments inline.

Typical signs:

- The Function encapsulates a multi-step behavior flow.
- The Function may return `Unit`, `Bool`, `UInt`, a Bundle, a tuple, or another type, but it is not primarily a Predicate Function or Bundle Assignment Helper.
- The Function groups related logic for readability, reuse, or local structure.
- The Function may include side effects, assignments, intermediate calculations, or a return value that is produced through several logical steps.

Template path:

`templates/chisel/function/extract-func.md`

Selection rule:

Choose this template as the general-purpose fallback only when the Function is clearly an extracted behavior block and does not fit a more specific existing category.

## Bundle Assignment Helper

Use this category when the Function's primary role is to create, fill, connect, update, or centralize assignment logic for a Chisel Bundle.

Typical signs:

- The Function assigns fields of a target Bundle.
- The Function returns a constructed or filled Bundle.
- The Function mutates or connects a Bundle argument, `this`, or an IO Bundle field.
- The Function exists to avoid repeating field-by-field Bundle assignment logic.

Template path:

`templates/chisel/function/bdl-assign-func.md`

Selection rule:

Choose this template when the important behavior is field assignment coverage for a Bundle. Every target Bundle field must be accounted for by the selected template's rules.

## Exception Handling

If no category above fits the Function accurately, stop and ask the user how to handle it.

Do not silently force the Function into the closest category.
Do not invent a new Function category or template path without user confirmation.
Do not omit the Function from the TECH spec if the parent template requires every Function to be described.

Ask the user to choose one of the following actions:

- Treat the Function as one of the existing categories and confirm which template to use.
- Add a new Function category and provide or approve the new template path.
- Leave the Function out of scope and confirm why it does not need a spec.
