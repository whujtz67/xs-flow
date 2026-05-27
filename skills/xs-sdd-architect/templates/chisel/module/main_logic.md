# Main Logic Spec Template

<!--
Use this template to describe one coherent Main Logic block inside the central Module.

This template is for files under:

`new/module/main_logic/ml_<main-logic-name>.md`

For simple Main Logic, use:

`new/module/main_logic/ml_main.md`

Each file must describe exactly one Main Logic block.

Do not describe FSM or Pipeline behavior here when that behavior is separated into its own FSM or Pipeline spec.
-->

## Main Logic Summary

| Item | Content |
|---|---|
| Main Logic Name | `<main-logic-name>` |
| Spec Path | `new/module/main_logic/ml_<main-logic-name>.md` |
| Belongs To | `<central-module-name>` |
| Purpose | `<what-this-logic-block-decides-or-assigns>` |

## Local Private Values (Optional)

<!--
List local `private val` definitions used inside this logic block.

Do not list simple one-use temporary expressions unless they clarify important control or datapath semantics.
-->

| Name | Type | Source | Meaning |
|---|---|---|---|
| `<private-val-name>` | `<complete-chisel-type>` | `<expression-or-source>` | `<meaning>` |

## Key Boolean Decisions (Optional)

<!--
Use this section for important Boolean decisions that control whether a key action happens.

For complex Boolean decisions, describe leaf-level Boolean conditions first, then describe how they are composed.

If the Boolean decision is already specified as a Predicate Function, do not duplicate the full Predicate Function spec here.
Reference that Function and briefly explain how this logic block uses its result.
-->

### Leaf-level Boolean Conditions

<!--
Record only leaf-level Boolean-valued expressions that directly participate in the final decision.
Do not record larger compound Boolean expressions joined by Boolean Operators.
-->

| ID | Condition | Type | Meaning |
|---|---|---|---|
| `C1` | `<condition>` | `Bool` | `<meaning>` |
| `C2` | `<condition>` | `Bool` | `<meaning>` |

### Decision Composition

<!--
Use condition IDs from the table above.
The Canonical Decision Expression must be fully parenthesized when needed to avoid ambiguity.
The Composition Description must preserve the same logical structure.
-->

**Canonical Decision Expression**

`<decision-name> = <canonical-decision-expression-using-condition-ids>`

**Composition Description**

```text
<decision-name>
└── <Boolean-Operator>
    ├── <condition-id>: <short-condition-meaning>
    └── <Boolean-Operator>
        ├── <condition-id>: <short-condition-meaning>
        └── <condition-id>: <short-condition-meaning>
```

## Conditional Behavior (Optional)

<!--
Use this section to describe `when` / `elsewhen` / `otherwise` behavior.

The condition space must be fully covered unless uncovered cases are intentionally covered by default assignments or by another confirmed behavior section.
If neither condition holds, add an explicit `otherwise` behavior or a condition coverage note.
-->

### Conditional Behavior Tree

```text
<logic-block-name>
└── when <condition-1>
    ├── Action: <assignment-or-update>
    ├── Action: <assignment-or-update>
    └── when <nested-condition-1>
        └── Action: <nested-assignment-or-update>
└── elsewhen <condition-2>
    └── Action: <assignment-or-update>
└── otherwise
    └── Action: <default-preserving-action-or-explicit-assignment>
```