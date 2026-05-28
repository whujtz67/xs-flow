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
- Public Bundles that need to be newly specified or modified for this Module.
- Private Bundles defined inside this Module.

If this Module does not use or define any Bundle, write `None.` under this section and omit all subsections.
-->

## Related Bundles

<!--
List Bundle references used by this Module.
All new, modified, or module-local Bundle bodies must be specified in separate Bundle spec files, not inline-expanded here.
Both subsections are optional. Omit any subsection that has no entries.
If this Module does not use any Bundle, write `None.` and omit all subsections.
-->

### Public Bundles

#### Existing Bundles

| Name | Meaning | Path |
|---|---|---|
| `<bundle-name>` | `<brief-meaning>` | `<relative-source-file-path>` |

#### Bundles to Modify

| Name | Meaning | Existing Path | Spec Path |
|---|---|---|---|
| `<bundle-name>` | `<brief-meaning>` | `<relative-source-file-path>` | `modify/b_<bundle-name>.md` |

#### New Bundles

| Name | Meaning | Spec Path |
|---|---|---|
| `<bundle-name>` | `<brief-meaning>` | `new/bundle/b_<public-bundle-name>.md` |

### Module-Local Private Bundles

| Name | Meaning | Spec Path |
|---|---|---|
| `<bundle-name>` | `<brief-meaning>` | `new/module/bundle/b_<module-local-bundle-name>.md` |

### Inline Anonymous Bundles

<!--
List inline anonymous `new Bundle { ... }` structures that are defined directly at their use site in final code.

These entries are not real named Bundle definitions.
They must not be implemented as Chisel classes, objects, or standalone Bundle files.
They are only local anonymous Bundle references used by this `top.md` file.

Use the following Anonymous Bundle ID format:

`anon-bundle:<scope>/<var-name>`

Recommended scopes:
- `io` for top-level IO anonymous Bundles.
- `wire` for anonymous Bundles used by Wire definitions.
- `reg` for anonymous Bundles used by Reg definitions.
- `local` for other local anonymous Bundles.

For `<var-name>`, use the associated variable or IO field name directly.
For example:
- `anon-bundle:io/req` for `io.req`
- `anon-bundle:wire/arbResult` for a local `arbResult` Wire
- `anon-bundle:reg/status` for a local `status` Reg

The Anonymous Bundle ID is a spec-only reference handle.
It is intentionally not a valid Chisel type name.

Omit this subsection if the Module does not use inline anonymous Bundles.
-->

#### `anon-bundle:<scope>/<var-name>`

<!--
Define the fields of this inline anonymous Bundle.

This table describes the body of a local `new Bundle { ... }` expression.
Do not add requirements, methods, helper functions, or standalone Bundle-level behavior here.
If such content is needed, this structure should be promoted to a normal Bundle spec instead.
-->

| Field | Type | Description |
|---|---|---|
| `<field-name>` | `<complete-chisel-type-or-construction-expression>` | `<description>` |

---

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
- For an IO field implemented with an inline anonymous `new Bundle { ... }`:
  - Do not expand the anonymous Bundle fields in this IO table.
  - Do not invent a normal Bundle name.
  - Use the corresponding Anonymous Bundle ID in the `Type` column.

For `Clock Domain`:
- If this Module has no asynchronous or cross-clock IO, use the table without `Clock Domain`.
-->

| Name | Direction | Type | Clock Domain (Optional) | Description |
|---|---|---|---|---|
| `<io-field-name>` | `Input` / `Output` / `Flipped` / `Unflipped` | `<chisel-type-or-anon-bundle-id>` | `<clock-domain-name-or-description>` | `<description>` |

---

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

## Shared Internal Signal Declaration

<!--
List module-scope internal signals that are shared by multiple downstream logic specs
or are required as common context for later behavior descriptions.

This section is not a dump of all local signals.
Include a signal here only if it satisfies at least one of the following:
- It is referenced, assigned, or updated by more than one major logic section, such as FSM, pipeline, main logic subsections.
- It holds module state or affects cross-section control flow, datapath selection, handshake timing, or behavior described in multiple later sections.
- It is declared at Module scope so that later sections can reference the same signal consistently.

Do not include signals that are purely local to one FSM, one pipeline stage, one main-logic block, or one small expression.
Such local-only intermediate signals must be declared in the corresponding local logic spec, not here.

All entries in this section are module-internal signals and should be implemented as `private val` bindings in final code.
The `Kind` column describes the underlying Chisel construction or expression category, not the Scala binding keyword.

This section covers shared Chisel declarations such as:
- Wires: `Wire(...)` and `WireDefault(...)`
- Regs : `Reg(...)`, `RegInit(...)`, `RegNext(...)` and `RegEnable(...)`
- Expression-derived internal signals, marked as `Derived Signal`

For `Kind`:
- Use `Wire`, `WireDefault`, `Reg`, `RegInit`, `RegNext`, or `RegEnable` for signals created by the corresponding Chisel construction.
- Use `Derived` for expression-derived internal signals that do not directly instantiate a Wire or Reg, such as `io.in.fire && readyCond`.
- Do not use `private val` as a `Kind`; `private val` is the expected Scala binding form for all entries, not a signal category.

For `Type`:
- Must be the complete Chisel type or construction expression or Anonymous Bundle ID.
- Do not write incomplete types such as `UInt`; write `UInt(<width>.W)`.
- For Vec, Bundle, or parameterized types, include complete constructor arguments.
- For `Derived Signal`, write the resolved Chisel type when clear, or the complete expression form if the type is expression-derived.
- The Impl AI should be able to directly reuse the type or expression information in code.

For `Initial / Default Value`:
- For plain `Wire(...)` or `Reg(...)` without default/reset value, write `None`.
- For `Derived Signal`, write `None`.
-->

| Name | Kind | Type | Initial / Default Value | Description |
|---|---|---|---|---|
| `<signal-name>` | `Wire` / `WireDefault` / `Reg` / `RegInit` / `RegNext` / `RegEnable` / `Derived` | `<chisel-type-or-anon-bundle-id>` | `<initial-or-default-value-or-None>` | `<description>` |

---

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

---

## State Machine (Optional)

<!--
List separated FSM specs used by this Module.

Each FSM body must be specified in a separate file under:

`new/module/fsm/`

Do not inline-expand FSM bodies here.

If this Module has no FSM, write `None.`
-->

| Name | Meaning | Spec Path |
|---|---|---|
| `<fsm-name>` | `<brief-meaning>` | `new/module/fsm/fsm_<fsm-name>.md` |

---

## Main Logic

<!--
List separated Main Logic specs used by this Module.

Each Main Logic body must be specified in a separate file under:

`new/module/main_logic/`

Do not inline-expand Main Logic bodies here.

Choose exactly one form below:
1. Use `ml_main.md` when the Module has one simple Main Logic spec.
2. Use multiple `ml_<main-logic-name>.md` entries when Main Logic is split into multiple blocks.

Delete the unused form.
-->

<!-- Form 1: Simple Main Logic -->

| Name | Meaning | Spec Path |
|---|---|---|
| `main` | `<brief-meaning>` | `new/module/main_logic/ml_main.md` |

<!-- Form 2: Split Main Logic Blocks -->

| Name | Meaning | Spec Path |
|---|---|---|
| `<main-logic-name-0>` | `<brief-meaning>` | `new/module/main_logic/ml_<main-logic-name-0>.md` |
| `<main-logic-name-1>` | `<brief-meaning>` | `new/module/main_logic/ml_<main-logic-name-1>.md` |

---

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

---

## Register Update

---

## Assertions 

<!-- TODO -->
