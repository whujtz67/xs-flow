# TECH Spec Set Structure

Write the generated TECH spec set under:

`${PRJ_DIR}/specs/<feature-rel-path>/TECH/`

## Directory Layout

The TECH spec set must use the following structure:

```text
${PRJ_DIR}/specs/<feature-rel-path>/TECH/
├── building-block-view.md
├── new/
│   ├── module/
│   │   ├── top.md
│   │   ├── fsm/        (optional)
│   │   │   ├── fsm_<fsm-name-0>.md
│   │   │   └── ...
│   │   ├── pipe/       (optional)
│   │   │   ├── pipe_<pipeline-name-0>.md
│   │   │   └── ...
│   │   ├── bundle/     (optional)
│   │   │   ├── b_<module-local-bundle-name-0>.md
│   │   │   └── ...
│   │   └── main_logic/
│   │       ├── ml_<main-logic-name-0>.md (or simplily ml_main.md when main logic is simple)
│   │       └── ...
│   └── bundle/         (optional)
│       ├── b_<public-bundle-name-0>.md
│       └── ...
└── modify/             (optional)
    ├── <code-structure-prefix>_<name>.md
    └── ...
```

## File Roles

### `building-block-view.md`

`building-block-view.md` is the entry file and assembly view of the TECH spec set.

It shows the static decomposition of the design into building blocks, such as Module, Bundle, Object, Trait, FSM, Pipeline, and Main Logic topics, as well as their dependency and reference relationships.

Architect AI and Impl AI must read `building-block-view.md` before loading other TECH spec files.

### `new/`

Use `new/` for complete specs of newly added code structures.

#### `new/module/`

Use `new/module/` for the central Module's TECH spec topics.

##### `new/module/top.md`

`new/module/top.md` is the top-level Module assembly spec.

It describes Module-level structure and anchors, such as Parameters, IO, Submodules, Wire/Reg declarations, Default Assignment, Output Assignment, Register Update, and references to separated FSM / Pipeline / Main Logic specs.

Do not place the full FSM, Pipeline, or Main Logic body in `top.md` when that logic is separated into its own topic file.

##### `new/module/fsm/`

Use `new/module/fsm/` for standalone FSM specs inside the central Module.

Each file must describe only one FSM:

`fsm_<fsm-name>.md`

##### `new/module/pipe/`

Use `new/module/pipe/` for standalone Pipeline specs inside the central Module.

Each file must describe only one Pipeline:

`pipe_<pipeline-name>.md`

##### `new/module/main_logic/`

Use `new/module/main_logic/` for separated Main Logic specs inside the central Module.

Each file must describe only one coherent Main Logic block:

`ml_<main-logic-name>.md`

For simple Main Logic, use one file such as:

`ml_main.md`

##### `new/module/bundle/`

Use `new/module/bundle/` for Module-local Bundles.

These Bundles are only used inside the central Module and do not have an independent public lifecycle.

Each file must describe one Module-local Bundle:

`b_<module-local-bundle-name>.md`

#### `new/bundle/`

Use `new/bundle/` for newly added public Bundle specs.

Each file must describe one public Bundle:

`b_<public-bundle-name>.md`

Public Bundles should be referenced from `building-block-view.md` and from the central Module spec when used.

### `modify/`

Use `modify/` for incremental specs of existing code structures.

Each modify spec must describe **ONLY** the newly added or modified design facts required by the current feature.

Do not rewrite, duplicate, or restate stable existing specs.

#### Code Structure Naming Prefixes

Use stable, readable prefixes for generated spec files.

For examples:

- `b_` for Bundle specs
- `m_` for all Module specs expect the newly added Module
- `f_` for standalone or separately specified Function specs when explicitly required
- `p_` for standalone or separately specified Parameter specs when explicitly required
- etc.