# Extract Method Function Spec Template

<!--
Extract Method extracts a long or comment-needed code block into a named Function. Its core idea is to extract a long block of code, or a block of code whose purpose would otherwise require explanatory comments, into a separate function, and to use the name of that function to clearly express its intent.
-->

{{H}} Function Summary

<!--
Describe the Function identity and extracted purpose.
-->

| Item | Content |
|---|---|
| Function Type | Extract Method |
| Function Declaration | <!-- Exact declaration, e.g. `private def updateEntry(entry: EntryBundle, req: ReqBundle): Unit`. --> |
| Function Semantics | <!-- What behavior this Function encapsulates. --> |
| Extraction Purpose | <!-- Why this logic is extracted instead of written inline. --> |
| Return Type | <!-- Exact return type, e.g. `Unit`, `Bool`, `UInt`, `TaskBundle`, or `(Bool, UInt)`. --> |

{{H}} Input Arguments (Optional)

<!--
Omit if the Function has no input arguments.
-->

| Argument | Type | Description |
|---|---|---|
| `<argument-name>` | `<argument-type>` | `<argument-description>` |

{{H}} Direct Callees (Optional)

<!--
Use this table to describe other Functions directly called inside the body of this function.
Omit if the Function calls no other Function.
-->

| Function | Function Type | Return Type | Dependency Description |
|---|---|---|---|
| `<function-name>` | `<function-type>` | `<return-type>` | `<how-this-function-uses-the-callee-result>` |

{{H}} Intermediate Variables (Optional)

<!--
Use this table to describe intermediate variables defined inside the function body.
Omit if the Function defines no meaningful intermediate variable.
-->

| Intermediate Variable | Type | Source | Meaning |
|---|---|---|---|
| `<intermediate-variable>` | `<type>` | `<how-this-variable-is-produced>` | `<meaning>` |

{{H}} Return (Optional)

<!--
Omit if the Function return type is `Unit`.
-->

| Output Variable | Type | Source  | Meaning |
|---|---|---|---|
| `<output-variable-or-return-value>` | `<type>` | `<how-this-output-is-produced>` | `<meaning-of-this-output>` |

{{H}} Behavior Description

<!--
Describe the extracted logic as a short behavior flow.
Choose steps according to the actual Function body.
For non-Unit Functions, show how the return value is produced.
For Unit Functions, show what side effect or assignment is completed.
-->

{{H+1}} Behavior Flow

```text
<function-name>
├── Step 1: <first-logical-stage>
├── Step 2: <next-logical-stage>
├── ...
└── Step N: <final-return-or-completed-side-effect>
```

{{H+1}} Step Details

<!--
Expand each step in the Behavior Flow.
Each Step Detail must correspond to one step above.
-->

{{H+2}} Step 1: `<first-logical-stage>`

`<explain-step-1-in-detail>`

{{H+2}} Step 2: `<next-logical-stage>`

`<explain-step-2-in-detail>`

{{H+2}} Step N: `<final-return-or-completed-side-effect>`

`<explain-final-step-in-detail>`