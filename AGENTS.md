# AGENTS.md

# Circuit Annotation — Agent Instructions

Circuit Annotation is a circuit drawing and annotation tool aimed primarily at
electrical/electronic engineering students.

Read `PROJECT_VISION.md` before making significant architectural changes.

If `ARCHITECTURE.md` exists, treat it as the source of truth for architecture.

---

## Core Project Principle

Circuit Annotation is NOT primarily a simulator.

Its purpose is to let users create clean electrical schematics and annotate them
with engineering-analysis notation such as:

- voltage polarity (+ / -)
- voltage labels
- current arrows
- KCL annotations
- KVL loop arrows
- arbitrary mathematical labels
- voltage spans across one or more components

Do not introduce simulation or symbolic circuit solving unless explicitly requested.

---

## Architectural Boundaries

Keep these concerns separate:

1. Circuit topology
2. Graphical layout
3. Annotations
4. Rendering
5. GUI

Do not combine them into monolithic classes.

### Circuit topology

The circuit model should contain electrical structure:

- components
- ports
- nodes
- connections

It should not depend on:

- SVG
- GUI widgets
- mouse coordinates
- rendering code

### Layout

Layout may contain:

- component positions
- component orientations
- wire routes
- spacing
- alignment
- layout constraints

Layout should not modify electrical connectivity.

### Annotations

Annotations are separate objects associated with circuit/layout entities.

Examples:

- current arrows
- voltage polarity
- voltage spans
- KVL loops
- node labels
- arbitrary labels

Do not bake annotation state directly into renderer-specific code.

### Rendering

SVG is the first rendering backend.

The renderer should consume the document/model and produce output.

Rendering code should not mutate the circuit topology.

### GUI

The GUI manipulates the underlying document model.

The GUI must not treat rendered SVG as the authoritative circuit representation.

---

## Source of Truth

The internal Circuit Document is the source of truth.

The eventual text/DSL interface and GUI should both modify the same underlying
document representation.

Do not create separate incompatible circuit representations for the GUI and DSL.

---

## Development Rules

Prefer small, focused changes.

Do not implement unrelated features while completing a task.

Do not perform large refactors unless explicitly requested or clearly necessary.

Before changing a public interface, search for existing usages and tests.

Avoid adding dependencies unless they provide substantial value.

Prefer readable code over clever code.

Use descriptive names.

Do not prematurely optimize.

---

## Testing

New non-trivial behavior should have tests.

Bug fixes should include a regression test when practical.

Run the relevant test suite before considering a task complete.

Do not delete or weaken tests merely to make a change pass.

---

## GUI Development

Keep application logic outside GUI event handlers whenever practical.

GUI classes should delegate behavior to model/controller/service code.

Avoid embedding circuit semantics directly into GUI widgets.

Moving a graphical component must not change its electrical connectivity.

---

## Geometry and Rendering

Use logical/model coordinates independently of screen pixels where practical.

Keep SVG generation deterministic.

Given the same document state, rendering should produce equivalent geometry.

Do not scatter hard-coded spacing values throughout the codebase.

Centralize visual constants such as:

- component size
- default spacing
- wire clearance
- annotation spacing
- margins

---

## Initial Visual Convention

Use ANSI-style zig-zag resistors by default.

The architecture should eventually allow alternative visual standards, but do not
build a full theming system prematurely.

---

## LaTeX / Mathematical Labels

Labels should be stored as semantic text, not baked into raster graphics.

The system should be designed so mathematical notation such as

    V_{R_1}
    I_C
    V_o

can eventually be rendered cleanly.

Do not tightly couple the document model to a particular LaTeX rendering library.

---

## Automatic Layout

Perfect automatic layout is NOT required initially.

Manual GUI placement is acceptable.

Automatic layout / "Clean Layout" should eventually improve:

- alignment
- spacing
- wire bends
- wire crossings
- orthogonal routing
- symmetry

Do not make sophisticated automatic layout a prerequisite for basic circuit editing.

---

## Current Development Priority

Build the smallest usable vertical slice first:

1. Create/open a circuit document.
2. Add basic components.
3. Connect components.
4. Move components while preserving connections.
5. Label components.
6. Add voltage polarity annotations.
7. Add current-arrow annotations.
8. Export SVG.

Do not expand the component library aggressively until this basic workflow is stable.

---

## When Unsure

Do not guess at major architectural decisions.

If a requested feature conflicts with the existing architecture or requires a
significant new architectural decision, explain the issue before implementing it.

Prefer preserving clean boundaries over rapidly adding features.
