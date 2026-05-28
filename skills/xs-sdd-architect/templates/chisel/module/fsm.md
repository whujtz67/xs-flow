---
spec_type: 'fsm'
topic_name: '<fsm-name>'
spec_path: 'new/module/fsm/fsm_<fsm-name>.md'
belongs_to: '<central-module-name>'
purpose: '<what-this-fsm-controls-or-sequences>'
---

# FSM Spec Template

<!--
Use this template for one Chisel FSM.

Assume a three-segment FSM:
1. next-state transition
2. FSM output
3. register update

This template only describes segment 1 and segment 2.

Register update is handled uniformly at the end of the Module spec, usually as
current-state <= next-state connections. Do not describe it again here.
-->

## External References (Optional)

<!--
List definitions used by this topic but defined outside this topic spec.

For SSOT (Single Source Of Truth), only record the name and definition location here.
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

## State List

<!--
Specify the state encoding format, then list every legal state with its exact
encoded value. Do not omit temporary or error states.
-->

**State Encoding Format**: `<binary>` / `<gray>` / `<one-hot>` / `<custom>`

| State | Value | Meaning |
|---|---|---|
| `<state-name>` | `<encoded-value>` | `<state-meaning>` |

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

## Transition Conditions

<!--
Define reusable transition conditions here.

For complex conditions, prefer referencing a Predicate Function spec instead of
rewriting large Boolean logic here.
-->

| ID | Condition | Type | Meaning |
|---|---|---|---|
| `C1` | `<cond-expression-or-predicate-function>` | `Bool` | `<meaning>` |
| `C2` | `<cond-expression-or-predicate-function>` | `Bool` | `<meaning>` |

## State Transition

<!--
Describe only next-state decisions.

Do not describe outputs, assignments, counters, next-value assignments, or state
actions here. Put them in FSM Output.

For each current state, list conditions in real Chisel priority order.

Use `Default` for ordinary state hold when no condition matches.

Do not invent high-priority negated hold conditions such as `!req.valid` before
`req.valid`.

List an explicit self-loop only when the transition logic really contains that
condition and it intentionally blocks lower-priority transitions.
-->

| ID | Current State | Priority | Condition | Next State | Description |
|---|---|---:|---|---|---|
| `T1` | `<current-state>` | `1` | `<cond-or-cond-id>` | `<next-state>` | `<transition-meaning>` |
| `T2` | `<current-state>` | `2` | `<cond-or-cond-id>` | `<next-state>` | `<transition-meaning>` |
| `T3` | `<current-state>` | `Default` | `default` | `<current-state-or-default-next-state>` | `<default-transition-meaning>` |

## FSM Output

<!--
Describe state-local behavior by state.

This section owns outputs, Wire assignments, Reg next-value assignments, counters,
valid/ready control, request/response generation, and side effects.

Do not describe state transition decisions here, except as conditions that affect
outputs or next-value assignments.
-->

### `<state-name>`

| Signal | Condition | Source | Description |
|---|---|---|---|
| `<signal>` | `Always` / `<condition>` | `<value-or-source>` | `<behavior-description>` |