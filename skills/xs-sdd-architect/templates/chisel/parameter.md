# Parameter Spec Template

<!--
Use this template to describe elaboration-time parameters used by a Chisel structure.
-->

{{H}} Parameters 

<!--
Describe the parameters used by this Bundle.
If this Bundle does not need any parameter description, write `None.` under this section and omit all subsections.

Use `require(<requirement-expression>, <failure-message>)` to enforce every parameter requirement.
-->

{{H+1}} Implicit Parameters (Optional)

<!--
Describe implicit parameters in prose.
Omit if this Bundle has no implicit parameters.
-->

`<implicit-parameter-description>`

{{H+1}} Input Parameters (Optional)

<!--
Omit if this Bundle has no input parameters.
Leave optional cells empty if not needed.
-->

| Name | Type | Meaning | Default (Optional) | Require (Optional) |
|---|---|---|---|---|
| `<parameter-name>` | `<parameter-type>` | `<meaning>` | `<default-value>` | `<requirement>` |

{{H+1}} Intermediate Parameters (Optional)

<!--
Describe intermediate parameters derived at elaboration time, such as `private val`s used to compute widths, sizes, types, etc.
Do not include Intermediate Hardware Variables, such as `Wire`, `Reg`, or hardware expressions.
Omit if this Bundle defines no meaningful intermediate parameter.
Leave optional cells empty if not needed.
-->

| Name | Type | Source | Meaning | Require (Optional) |
|---|---|---|---|---|
| `<parameter-name>` | `<parameter-type>` | `<how-this-parameter-is-derived>` | `<meaning>` | `<requirement>` |