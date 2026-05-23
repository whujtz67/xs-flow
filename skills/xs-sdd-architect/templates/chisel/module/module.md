# Module Spec Template

<!--
Use this template to describe one Chisel Module.

A Module is the primary structural unit where Bundles, Parameters, IO, Submodules,state, control logic, behavior, etc are integrated. 

This template must be written with enough precision that the Impl AI can directly map the spec to Chisel code.
-->

## Related Bundles

<!--
List all Bundles related to this Module.

Include:
- Public Bundles directly used by this Module.
- Public Bundles that need to be newly specified or extended for this Module.
- Private Bundles defined inside this Module.

If this Module does not use or define any Bundle, write `None.` under this section and omit all subsections.
-->

### Public Bundles

<!--
Public Bundles are reusable Bundle definitions outside this Module.
-->

#### Existing Bundles

<!--
List existing public Bundles that are already defined and can be reused directly.

Do not regenerate their specs here. Use `Path` to point to the relative source file path where the Bundle is defined, so that the Impl AI can locate it.
-->

| Name | Meaning | Path |
|---|---|---|
| `<bundle-name>` | `<brief-meaning>` | `<relative-source-file-path>` |

#### Bundles Requiring New Specs

<!--
List public Bundles whose Module-related requirements need a separate Bundle spec.

This includes:
- New Bundles that have not been implemented yet.
- Existing Bundles that need additional fields, behavior, or feature extensions for this Module.

For each listed Bundle, create a new Bundle spec file according to `templates/chisel/bundle.md`.

If the Bundle is a new definition, the new spec should describe the complete Bundle.

If the Bundle is an extension to an existing Bundle, the new spec must describe only the newly added or modified part required by this Module. Do not edit, rewrite, or duplicate the existing stable Bundle spec.
-->

| Name | Meaning | Attr | Spec Path |
|---|---|---|---|
| `<bundle-name>` | `<brief-meaning>` | `new` / `update` | `<relative-path-of-new-bundle-spec>` |

### Private Bundles (Optional)

<!--
Private Bundles are Bundle-like structures defined only for use inside this Module,
for example through a private helper definition inside the Module.

Private Bundles do not need standalone spec files.
For each Private Bundle, add one subsection below and inline-expand the relevant
parts of `templates/chisel/bundle.md` directly in this Module spec.

Omit this section if the Module has no Private Bundle.
-->

#### `<private-bundle-name>`

<!-- Inline-expand `templates/chisel/bundle.md` here. -->

## Parameters

<!--
Describe all implicit, input and derived elaboration-time parameters used by this Module.

Use `templates/chisel/parameter.md` and expand it directly in this section.

If this Module does not use any parameter, write `None.` under this section.
-->

<!-- Inline expansion of `templates/chisel/parameter.md` starts here. -->

## IO

<!--
List all top-level IO ports of this Module.

For `Direction`:
- For ordinary leaf signals, use `Input` or `Output`.
- For mixed-direction Chisel interfaces such as `Valid`, `Decoupled`, or custom
  Bundles:
  - Use `Flipped` for io fields declared with `Flipped(...)`.
  - Use `Un-Flipped` for io fields declared without `Flipped(...)`.

For `Type`:
- Must be the complete Chisel type or construction expression used in final code. The Impl AI should be able to copy the `Type` expression into code without modification.

For `Clock Domain`:
- If this Module has no asynchronous or cross-clock IO, use the table without `Clock Domain`.
-->

| Name | Direction | Type | Clock Domain (Optional) | Description |
|---|---|---|---|---|
| `<io-field-name>` | `Input` / `Output` / `Flipped` / `Unflipped` | `<complete-chisel-type-or-construction-expression>` | `<clock-domain-name-or-description>` | `<description>` |

## Submodules

<!--
List all Submodules instantiated inside this Module.

If this Module does not instantiate any Submodule, write `None.` under this section.

For `Instance Name`:
- Use the actual instance variable name in the Module.

For `Type`:
- Use the complete Chisel construction expression.
- Prefer expressions that can be directly used after `=`, such as
  `Module(new <SubmoduleType>(<args>))`.

For `Instance Num`:
- Use `1` for a single instance.
- Use the exact number or parameter expression for generated instances, such as
  `<nBanks>`, `<nWays>`, or `<numEntries>`.
-->

| Instance Name | Type | Instance Num | Description |
|---|---|---|---|
| `<instance-name>` | `<complete-chisel-module-construction-expression>` | `<instance-num-or-parameter-expression>` | `<description>` |

## Wire/Reg Declaration

## Wire/Reg Declaration

<!--
List important internal Wire and Reg declarations in this Module.

This section covers Chisel declarations such as:
- `Wire(...)`
- `WireDefault(...)`
- `Reg(...)`
- `RegInit(...)`
- `RegNext(...)`
- `RegEnable(...)`

For simple temporary expressions that are purely local and obvious, this section
may omit them. Focus on internal signals that affect datapath, control flow,
state holding, handshake timing, or later behavior descriptions.

For `Type`:
- Must be the complete Chisel type or construction expression.
- Do not write incomplete types such as `UInt`; write `UInt(<width>.W)`.
- For Vec, Bundle, or parameterized types, include complete constructor arguments.
- The Impl AI should be able to directly reuse the type expression in code.

For `Initial / Default Value`:
- For plain `Wire(...)` or `Reg(...)` without default/reset value, write `None`.

For `Update / Assignment Rule`:
- For Wire, describe how it is assigned or conditionally overridden.
- For Reg, describe when and how it is updated after reset.
- For `RegNext(...)` or `RegEnable(...)`, describe the next-value source and enable condition.
-->

| Name | Kind | Type | Initial / Default Value | Update / Assignment Rule | Description |
|---|---|---|---|---|---|
| `<signal-name>` | `Wire` / `WireDefault` / `Reg` / `RegInit` / `RegNext` / `RegEnable` | `<complete-chisel-type-or-construction-expression>` | `<initial-or-default-value-or-None>` | `<assignment-or-update-rule>` | `<description>` |

## Default Assignment

<!--
Describe default assignments for IO outputs and internal Wire signals that need
explicit defaults.

In Chisel, default assignments are usually written before conditional overrides.
Later connections in the same elaborated scope may override earlier connections,
so this section should describe the intended default-before-override structure.

Do not repeat defaults that are already expressed by `WireDefault(...)` in
Wire/Reg Declaration.

This section usually covers:
- Module IO output defaults.
- Internal `Wire(...)` signals without defaults but requiring safe default values.

This section usually does not cover next-state pair defaults such as
`a_nxt := a_r`. For register/next-value pairs, prefer the project coding style:

`private val a_nxt = WireDefault(a_r)`

and describe the pair in Wire/Reg Declaration instead.
-->

### Zero-like Default Assignments

<!--
Most default assignments are zero-like values. To keep the spec compact, groupthese signals together.

For implementation, the Impl AI should choose the concrete zero-like expression
according to each signal's type, such as:
- `0.U`
- `0.S`
- `false.B`
- `0.U.asTypeOf(<type>)`
- an idle enum value
-->

The following signals use zero-like default assignments:

`<signal-a>`, `<signal-b>`, `<signal-c>`

### Non-zero Default Assignments (Optional)

<!--
Use this section only for signals whose default value is not the natural
zero/false/idle value.

Use an ordered list so that each non-zero default can be explained precisely.
-->

1. `<signal-name> := <default-value>`

   `<default-meaning>`

2. `<signal-name> := <default-value>`

   `<default-meaning>`

## Main Logic

<!--
Describe the main combinational/control logic of this Module.

This section should not include FSM or Pipeline behavior when those behaviors should be described in their own sections.

Main Logic may be organized flexibly according to the actual Module structure.
Use small subsections to describe coherent pieces of logic.

Do not force every subsection to include every part. Keep simple logic simple,
but make key control conditions explicit.
-->

### `<main-logic-block-name>`

<!--
Use one subsection for each coherent logic block.

The subsection title should describe the logic purpose, not merely restate an
implementation detail. For example:
- Request Selection
- Output Response Generation
- Bypass Decision
-->

#### Purpose

<!--
Briefly describe what this logic block decides or assigns.
-->

#### Local Private Values (Optional)

<!--
List local `private val` definitions used inside this logic block.

These values are usually extracted to improve readability, reuse, or to name
important intermediate expressions.

Do not list simple one-use temporary expressions unless they clarify important
control or datapath semantics.
-->

| Name | Type | Source | Meaning |
|---|---|---|---|
| `<private-val-name>` | `<complete-chisel-type>` | `<expression-or-source>` | `<meaning>` |

#### Key Boolean Decisions (Optional)

<!--
Use this section for important Boolean decisions that control whether a key
action happens.

This is especially useful for signals such as:
- `canFlow`
- `canAccept`
- `conflict`
- `needFlush`
- `stall`

For complex Boolean decisions, describe leaf-level Boolean conditions first, then
describe how they are composed.

If the Boolean decision is already specified as a Predicate Function in the
Function section, do not duplicate the full Predicate Function spec here.
Instead, reference that Function and briefly explain how this logic block uses
its result.
-->

##### Leaf-level Boolean Conditions

<!--
Use this table to describe the leaf-level Boolean conditions that compose the
final Boolean decision.

A Boolean Condition means a leaf-level Boolean-valued expression before it is
combined with other Boolean-valued expressions by Boolean Operators. Boolean
Operators include `AND`, `OR`, `NOT`, `XOR`, `XNOR`, `NAND`, and `NOR`.

A Boolean Condition may be:
- A comparison result, such as `a === b` or `a > b`;
- A Boolean field, such as `a.needRelease`;
- A reduction result, such as `Cat(vec).orR`;
- A direct Predicate Function call result, such as `addrConflict(a, s)`.

Record it as a Boolean Condition only when it directly participates in the final
decision.

Do not record larger compound Boolean expressions joined by Boolean Operators.
For example, record `a.tag === s.metaTag` and `s.needRelease` separately, but do
not record `(a.tag === s.metaTag) && s.needRelease` as a Boolean Condition.
-->

| ID | Condition | Type | Meaning |
|---|---|---|---|
| `C1` | `<condition>` | `Bool` | `<meaning>` |
| `C2` | `<condition>` | `Bool` | `<meaning>` |

##### Decision Composition

<!--
Use this section to describe how the Boolean Conditions are combined to produce
the final Boolean decision.

First provide a fully parenthesized Canonical Decision Expression using the
condition IDs defined in the Boolean Conditions table. The expression must
preserve the exact logical structure and avoid ambiguity from operator
precedence.

Then provide a Composition Description as a logic-tree-style structure using
Boolean Operator nodes. The Composition Description should use condition IDs and
short semantic labels to show how the Boolean Conditions are combined. It should
not repeat the full Boolean expression by default, because the exact expression
binding is already defined in the Boolean Conditions table.

The Composition Description must preserve the nesting relationship between
different Boolean Operators and must remain equivalent to the Canonical Decision
Expression.
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

#### Conditional Behavior (Optional)

<!--
Use this section to describe `when` / `elsewhen` / `otherwise` behavior.

Prefer a tree-style structure over nested unordered lists because Chisel
conditional logic is priority-ordered and may be nested. The tree should make
the following information explicit:
- Condition order.
- Nesting relationship.
- Actions under each branch.
- Whether each branch overrides any Default Assignment.

Every condition branch should describe the observable assignment or state update
caused by that branch.

The condition space must be fully covered unless one of the following is true:
1. The uncovered cases are intentionally covered by Default Assignment, and the
   user has confirmed that all cases other than the listed `when` / `elsewhen`
   branches should keep the default value.
2. The behavior of other possible condition spaces is described later in another
   section, and the user has confirmed that all remaining cases should keep the
   default value.

If neither condition holds, explicitly add an `otherwise` behavior or a condition
coverage note explaining the missing condition space.
-->

##### Conditional Behavior Tree

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

## Output Assignment

<!--
Connect remaining straightforward output ports that are not assigned in Main Logic,
FSM Logic, Pipeline Logic, or other behavior sections.

Do not repeat outputs already assigned elsewhere.
If there is no remaining output assignment, write `None.`
-->

| Output | Source | Description |
|---|---|---|
| `<io.output-name>` | `<source-or-expression>` | `<description>` |
