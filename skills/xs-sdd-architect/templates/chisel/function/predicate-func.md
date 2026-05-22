# Predicate Function Spec Templates

<!--
Predicate functions are functions that return a single `true.B` or `false.B`. You use predicate functions to check if your input meets some condition.

In Chisel, `fire` can be treated as a common Predicate Function: when `valid` and `ready` are both true, `fire` returns `true.B`, indicating that a valid handshake occurs in the current cycle; otherwise, it returns `false.B`.

Use this template for a Function whose primary role is to produce a single Boolean decision that represents whether a specific RTL condition holds.
-->

## Function Summary

<!--
Use this table to describe the basic semantics of the Predicate Function.
-->

| Item | Content |
|---|---|
| Function Type | Predicate Function |
| Function Declaration | <!-- Provide the exact Function declaration as the anchor between the specification and the code, for example: `def conflict(a: TaskBundle): Bool`. If this Function is only called inside the current code structure, include the `private` prefix according to its actual visibility. --> |
| Function Semantics | <!-- Describe what condition this Function checks. --> |
| True Semantics | <!-- Describe what hardware condition is established, or what behavior is allowed / triggered, when this Function returns true. --> |
| False Semantics | <!-- Describe what hardware condition is not established, or what behavior is disallowed / not triggered, when this Function returns false. --> |

## Input Arguments (Optional)

<!--
Omit if the Function has no input arguments.
-->

| Argument | Type | Description |
|---|---|---|
| `<argument-name>` | `<argument-type>` | `<argument-description>` |

## Direct Callees (Optional)

<!--
Use this table to describe other Functions directly called inside the body of this function.
Omit if the Function calls no other Function.
-->

| Function | Function Type | Return Type | Dependency Description |
|---|---|---|---|
| `<function-name>` | `<function-type>` | `<return-type>` | `<describe-how-this-predicate-function-uses-the-callee-result>` |

## Intermediate Variables (Optional)

<!--
Use this table to describe intermediate variables defined inside the function body.
Omit if the Function defines no meaningful intermediate variable.
-->

| Intermediate Variable | Type | Source | Meaning |
|---|---|---|---|
| `<intermediate-variable>` | `<type>` | `<how-this-variable-is-produced>` | `<meaning>` |

## Primary Expressions

<!--
Use this table to describe the referenced primary expressions that form the leaf-level participants of the final Boolean expression returned by the Predicate Function.

A primary expression may be a signal, Bundle field, state field, register field, constant, parameter, or direct function / method call result that participates in the final Boolean decision.

Do not record larger compound Boolean expressions in this table, such as `a.tag === s.metaTag` or `(a.tag === s.reqTag || a.tag === s.metaTag && s.needRelease)`. Instead, record their primaries, such as `a.tag`, `s.reqTag`, `s.metaTag`, and `s.needRelease`.
-->

| Primary Expression | Type | Meaning |
|---|---|---|
| `<primary-expression>` | `<type>` | `<meaning>` |

## Leaf-level Boolean Conditions

<!--
Use this table to describe the leaf-level Boolean conditions that compose the final Boolean decision returned by the Predicate Function.

In this template, a Boolean Condition means a leaf-level Boolean-valued expression before it is combined with other Boolean-valued expressions by Boolean Operators. Boolean Operators include `AND`, `OR`, `NOT`, `XOR`, `XNOR`, `NAND`, and `NOR`.

A Boolean Condition may be a comparison result, Boolean field, reduction result, or direct Predicate Function call result. For example, `a === b`, `a > b`, `a.needRelease`, and `Cat(vec).orR` may all be recorded as Boolean Conditions when they directly participate in the final decision.

A Boolean Condition may be:
- A comparison result, such as `a === b` or `a > b`;
- A Boolean field, such as `a.needRelease`;
- A reduction result, such as `Cat(vec).orR`;
- A direct Predicate Function call result, such as `addrConflict(a, s)`.

Record it as a Boolean Condition only when it directly participates in the final decision.

Do not record larger compound Boolean expressions joined by Boolean Operators. For example, record `a.tag === s.metaTag` and `s.needRelease` separately, but do not record `(a.tag === s.metaTag) && s.needRelease` as a Boolean Condition.
-->

| ID | Condition | Type | Meaning |
|---|---|---|---|
| `C1` | `<condition>` | `Bool` | `<meaning>` |
| `C2` | `<condition>` | `Bool` | `<meaning>` |


## Behavior Description

<!--
Use this section to describe how the Predicate Function combines its Boolean Conditions to produce the final true/false return value.

First provide a fully parenthesized Canonical Decision Expression using the condition IDs defined in the Boolean Conditions table. The expression must preserve the exact logical structure of the Predicate Function and avoid ambiguity from operator precedence.

Then provide a Composition Description as a logic-tree-style structure using Boolean Operator nodes. The Composition Description should use condition IDs and short semantic labels to show how the Boolean Conditions are combined. It should not repeat the full Boolean expression by default, because the exact expression binding is already defined in the Boolean Conditions table.

The Composition Description must preserve the nesting relationship between different Boolean Operators and must remain equivalent to the Canonical Decision Expression.
-->

**Canonical Decision Expression**

`<function-name> = <canonical-decision-expression-using-condition-ids>`

**Composition Description**

```text
<function-name>
└── <Boolean-Operator>
    ├── <condition-id>: <short-condition-meaning>
    └── <Boolean-Operator>
        ├── <condition-id>: <short-condition-meaning>
        └── <condition-id>: <short-condition-meaning>
```

<!--
Example:

**Canonical Decision Expression**

`addrConflict = C1 && (C2 || (C3 && C4)) && NOT(C5 || (C6 XOR C7) || XNOR(C8, C9) || NAND(C10, C11) || NOR(C12, C13))`

**Composition Description**

```text
addrConflict
└── AND
    ├── C1: <meaning-of-C1>
    ├── OR
    │   ├── C2: <meaning-of-C2>
    │   └── AND
    │       ├── C3: <meaning-of-C3>
    │       └── C4: <meaning-of-C4>
    └── NOT
        └── OR
            ├── C5: <meaning-of-C5>
            ├── XOR
            │   ├── C6: <meaning-of-C6>
            │   └── C7: <meaning-of-C7>
            ├── XNOR
            │   ├── C8: <meaning-of-C8>
            │   └── C9: <meaning-of-C9>
            ├── NAND
            │   ├── C10: <meaning-of-C10>
            │   └── C11: <meaning-of-C11>
            └── NOR
                ├── C12: <meaning-of-C12>
                └── C13: <meaning-of-C13>
```
-->