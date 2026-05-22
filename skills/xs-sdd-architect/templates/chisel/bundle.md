# Bundle Spec Template

<!--
Use this template to describe a Chisel Bundle, including its parameters, fields, and Functions.
-->

## Parameters

<!--
Describe the parameters used by this Bundle.
If this Bundle does not need any parameter description, write `None.` under this section and omit all subsections.
-->

### Implicit Parameters (Optional)

<!--
Describe implicit parameters in prose.
Omit if this Bundle has no implicit parameters.
-->

`<implicit-parameter-description>`

### Input Parameters (Optional)

<!--
Omit if this Bundle has no input parameters.
Leave optional cells empty if not needed.
-->

| Name | Type | Meaning | Default (Optional) | Require (Optional) |
|---|---|---|---|---|
| `<parameter-name>` | `<parameter-type>` | `<meaning>` | `<default-value>` | `<requirement>` |

### Intermediate Variables (Optional)

<!--
Describe private intermediate variables, such as `private val`s.
Omit if this Bundle defines no meaningful intermediate variable.
Leave optional cells empty if not needed.
-->

| Name | Type | Source | Meaning | Require (Optional) |
|---|---|---|---|---|
| `<variable-name>` | `<variable-type>` | `<how-this-variable-is-produced>` | `<meaning>` | `<requirement>` |

## Fields

<!--
Describe all fields defined by this Bundle.
If a field Type is another parameterized Bundle, include its actual parameters in Type.
-->

| Name | Type | Meaning |
|---|---|---|
| `<field-name>` | `<field-type>` | `<field-meaning>` |

## Functions (Optional)

<!--
Describe Functions defined inside this Bundle.

Each Function in this Bundle must be described.
For each Function, add one subsection here using the corresponding Function Spec Template.

For each Function, select the corresponding Function Spec Template through:
`templates/chisel/function/function.md`

That file provides the function type classification rules and the path to each specific Function template.
Omit this section if this Bundle defines no Function.
-->