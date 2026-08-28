# Circuit-Annotation

Circuit Annotation — Founding Context

You are working with the user on a project called Circuit Annotation.

The idea came from a very simple frustration: electrical-engineering students regularly need to draw circuits for homework, derivations and laboratory reports, but existing schematic programs are largely designed around simulation, PCB design, or manual schematic entry. They are not particularly good at producing circuits specifically for explaining circuit analysis.

Circuit Annotation is intended to solve that.

What the program should become

Circuit Annotation is a standalone schematic creation and annotation tool aimed primarily at electrical/electronic engineering students.

The user should be able to construct a circuit through a graphical editor, and eventually also through a concise textual circuit description.

These two interfaces must operate on the same underlying circuit representation.

The program is not a simulator.

At least initially, it should not care what numerical voltage or current a circuit produces. Its purpose is representation, layout and annotation.

The core distinction

Do not accidentally turn this into:

LTspice
KiCad
Multisim
a PCB editor
a SPICE frontend
merely another Python circuit-drawing package

The defining feature is that the schematic is intended to communicate electrical reasoning.

A student should be able to draw a circuit and then annotate that circuit cleanly with the things normally scribbled onto paper while doing circuit analysis.

First-class annotations

Components should support voltage polarity directly.

For example, a resistor may have:

      +  v_R  -
─────/\/\/\/─────

The + and - orientation must be independently selectable rather than inferred permanently from component orientation.

Labels must support mathematical notation, preferably LaTeX-style input such as:

V_{R_1}
I_C
V_o
\Delta V

Current annotations need directional arrows whose direction can be explicitly chosen.

KVL annotations should support curved loop arrows inside circuit loops, with labels if desired.

Voltage annotations must not be restricted to one component. A voltage may span:

one component
several components
arbitrary nodes

so something equivalent to:

      +           V_x           -
      <─────────────────────────>

──R1────R2────R3────R4──────────

should eventually be possible.

Arbitrary labels and mathematical annotations should also be possible.

Circuit construction

The GUI matters.

The user should be able to place/connect components graphically when that is easier than describing them textually.

However, the GUI should manipulate a structured circuit model rather than directly treating SVG as the source of truth.

Eventually, there should also be a textual representation.

The desired language should sit somewhere between verbose Python code and an extremely terse netlist.

Do not prematurely lock down the DSL syntax.

Design the underlying model first.

Layout

Perfect automatic circuit layout is not an initial requirement.

Initially, allow the GUI to determine or influence placement.

A particularly promising feature is a Circuit Cleanup / Clean Layout operation.

It could progressively handle:

component alignment
consistent margins
consistent spacing
orthogonal wires
reducing unnecessary bends
reducing crossings
keeping labels clear
sensible ground/source/output orientation
eventually symmetry detection

Symmetry is desirable because many analog circuits are inherently symmetric, but it is not an early priority.

Avoid making automatic graph layout the blocker that prevents the rest of the application from being built.

Components

Initial/common component support should eventually include:

resistors
capacitors
inductors
voltage sources
current sources
ground
op-amps
diodes
BJTs
MOSFETs
switches
dependent sources

Do not attempt all of these at once.

A tiny component set with an excellent architecture is substantially more valuable than twenty components implemented badly.

Visual conventions

Initial resistor style should be ANSI zig-zag:

──/\/\/\/──

IEC rectangular resistors and other drawing conventions may later become selectable styles.

Output

SVG is the first rendering target.

This is deliberate.

SVG provides:

vector output
excellent report quality
straightforward geometry
browser rendering
easy inspection/debugging
later conversion to PDF/PNG
potential interactivity

PDF, PNG and perhaps CircuitikZ/TikZ can come later.

Application form

The long-term product is likely a standalone application, potentially with integration into VS Code.

One particularly attractive future workflow would be:

circuit source
       +
live graphical editor/preview

where editing either representation updates the same underlying circuit document.

Do not build a VS Code extension before the core library and document model work independently.

Equation generation

Automatic KCL/KVL equation generation is interesting but OFF THE ROADMAP FOR NOW.

The topology should ideally be designed such that this could be added later, but do not allow symbolic analysis to distract from the original purpose.

Similarly, simulation may someday exist through an optional backend, but Circuit Annotation must not depend on simulation.

Architectural rule worth protecting

Maintain a strong distinction between:

electrical topology
graphical layout
annotation
rendering
UI

Ideally:

Circuit Model
    │
    ├── Components
    ├── Ports
    ├── Nodes
    └── Connections

Layout Model
    │
    ├── positions
    ├── orientation
    ├── wire paths
    └── constraints

Annotation Model
    │
    ├── currents
    ├── voltages
    ├── polarity
    ├── KVL loops
    └── labels

Renderer
    │
    └── SVG

GUI
    │
    └── edits all of the above through defined APIs

Do not let component classes become giant objects containing circuit behaviour, mouse-event handling, SVG strings, layout heuristics and annotations all at once.

That will destroy this project as it grows.

Development philosophy

Much of this project may be developed with AI/Codex.

Therefore:

architecture and tests matter more than usual.

AI can generate a large amount of code extremely quickly. That means it can also generate a large amount of architectural debt extremely quickly.

Prefer small milestones.

Do not ask an agent to "implement Circuit Annotation."

Ask it to implement things such as:

the Node abstraction
two-terminal component ports
SVG resistor geometry
selection state in the editor
orthogonal wire representation
voltage annotation geometry

separately.

Every meaningful behaviour should gradually acquire regression tests.

Initial milestone

Resist the urge to start with KVL arrows, op-amps and beautiful automatic layout.

The first useful proof should be approximately:

1. Open application.

2. Place two resistors.

3. Connect them.

4. Move them.

5. Connection remains attached.

6. Give one resistor a label.

7. Add + and - polarity annotation.

8. Add a current arrow.

9. Export the result as SVG.

If this works cleanly, the fundamental architecture is probably viable.

Everything impressive comes afterward.

Why this project exists

Never lose sight of the original interaction:

A student is solving KCL or KVL and thinks:

"I just want to draw exactly what I have on this piece of paper, except clean enough to put in my report."

Circuit Annotation should make that absurdly easy.

That is the product.
