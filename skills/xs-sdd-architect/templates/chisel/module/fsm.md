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

## State List

<!--
Specify the state encoding format, then list every legal state with its exact
encoded value. Do not omit temporary or error states.
-->

**State Encoding Format**: `<binary>` / `<gray>` / `<one-hot>` / `<custom>`

| State | Value | Meaning |
|---|---|---|
| `<state-name>` | `<encoded-value>` | `<state-meaning>` |

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