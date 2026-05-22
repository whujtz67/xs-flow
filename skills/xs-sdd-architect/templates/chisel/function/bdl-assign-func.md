# Bundle Assignment Helper Function Spec Template

<!--
Bundle Assignment Helper centralizes Bundle field assignment logic into a named Function.
Use this template when the Function mainly creates, fills, connects, or updates a Bundle.
-->

## Function Summary

<!--
Describe the Function identity and assignment purpose.
-->

| Item | Content |
|---|---|
| Function Type | Bundle Assignment Helper |
| Function Declaration | <!-- Exact declaration, e.g. `private def assignResp(resp: RespBundle, req: ReqBundle): Unit`. --> |
| Function Semantics | <!-- What Bundle assignment logic this Function encapsulates. --> |
| Assignment Target | <!-- The Bundle being assigned, e.g. returned Bundle, input Bundle, or `this`. --> |
| Return | <!-- Returned value description, including name, type, source, and meaning. Use `Unit` if the Function returns nothing. --> |

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
| `<function-name>` | `<function-type>` | `<return-type>` | `<how-this-function-uses-the-callee-result>` |

## Intermediate Variables (Optional)

<!--
Use this table to describe intermediate variables defined inside the function body.
Omit if the Function defines no meaningful intermediate variable.
-->

| Intermediate Variable | Type | Source | Meaning |
|---|---|---|---|
| `<intermediate-variable>` | `<type>` | `<how-this-variable-is-produced>` | `<meaning>` |

## Behavior Description

<!--
Describe how each assigned field is driven.

Every target Bundle field must be accounted for.
If any field has no specified assignment rule, do not:
- Silently fill the gap with your own assumption.
- Silently place it under Unassigned Fields either.

Ask the user to confirm whether the field was omitted by mistake or is intentionally unassigned.
Only confirmed intentionally-unassigned fields may be listed under Unassigned Fields.
-->

| Target Field | Source / Assignment Rule | Condition | Meaning |
|---|---|---|---|
| `<target-field>` | `<source-expression-or-assignment-rule>` | `<always / condition>` | `<meaning-of-this-assignment>` |

**Unassigned Fields**:

<!--
Omit only if all target fields are assigned.
A field may appear here only after confirming it is intentionally unassigned.
Do not connect unassigned fields to `DontCare` unless explicitly required.
-->

`<field-name>`, `<field-name>` are not assigned in this Function because `<reason>`.

Or:

All other fields are not assigned in this Function because `<reason>`.