---
spec_type: 'main_logic'
topic_name: '<main-logic-name>'
spec_path: 'new/module/main_logic/ml_<main-logic-name>.md'
belongs_to: '<central-module-name>'
purpose: '<what-this-logic-block-decides-or-assigns>'
---

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

## External References (Optional)

<!--
List definitions used by this topic but defined outside this topic spec.

For SSOT, only record the name and definition location here.
Do not repeat descriptions, fields, behavior, or semantics here.
Omit any subsection that has no entries.
If this topic has no external references, write `None.` and omit all subsections.
-->

### Referenced Bundles

| Name | Defined In |
|---|---|
| `<bundle-name-or-anon-bundle-id>` | `<definition-location>` |

### Referenced Functions

| Name | Defined In |
|---|---|
| `<function-name>` | `<definition-location>` |

### Referenced Signals

| Name | Defined In |
|---|---|
| `<signal-name>` | `<definition-location>` |

## Topic-local Signal Declaration (Optional)

<!--
Declare only signals local to this topic spec.

Do not include Module-level shared signals; those belong to `Shared Internal Signal Declaration`.
Do not include trivial one-use temporary expressions.

All entries should be implemented as `private val` bindings.
`Kind` describes the underlying signal category, not the Scala binding keyword.
-->

| Name | Kind | Type | Initial / Default Value | Description |
|---|---|---|---|---|
| `<signal-name>` | `kind` | `<chisel-type-or-anon-bundle-id>` | `<initial-or-default-value-or-None>` | `<description>` |

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