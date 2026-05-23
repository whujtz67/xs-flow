# Bundle Spec Template

<!--
Use this template to describe a Chisel Bundle, including its parameters, fields, and Functions.
-->

`<path-of-the-bundle>`

## Parameters

<!--
Describe this Bundle's parameters using:
`templates/chisel/parameter.md` from this skill's directory

That file provides the template for implicit parameters, input parameters, intermediate parameters, and parameter requirements.
If this Bundle does not need any parameter description, write `None.` under this section.
-->

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
`templates/chisel/function/entry.md` from this skill's directory

That file provides the function type classification rules and the path to each specific Function template.

Omit this section if this Bundle defines no Function.
-->