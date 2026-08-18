# Physics & Collision Pipeline

Actuasim's simulation core steps the world forward in discrete time increments, resolving gravity, contacts, and constraints so that anything built in the node graph behaves physically rather than just visually.

## Simulation Loop

Each step of the simulation:

1. Applies external forces (gravity, actuator commands from the node graph)
2. Advances kinematic/dynamic state
3. Detects and resolves collisions and contacts
4. Publishes updated state back onto the graph (joint states, contact events, transforms) for downstream nodes to consume

## Configurable World Parameters

- **Gravity** : defaults to Earth gravity along the vertical axis, but is configurable per-project
- **Time step** : controls simulation update rate independent of rendering rate (Update rates were capped to keep performance stable on application and rendering)
- **Solver iterations** : trades accuracy against performance for collision/constraint resolution

## Running the Graph

Simulation execution is controlled from within the graph itself. `Run` and `Stop` nodes (or any other input control the main running/enable logic) gate which parts of a project are actively stepping, the same way any other node's output gates downstream behavior; a project can wire up several of these, each starting or stopping a different subsystem on its own rather than through one shared switch.

Each active step advances by the configured time step (see *Configurable World Parameters* above), so timing stays consistent across whichever parts of the graph happen to be running at a given moment.

## Collision Handling

Collision detection and response is what turns a set of independently-moving bodies into a coherent scene; bodies that shouldn't interpenetrate don't, and contact events are surfaced back onto the node graph so control logic can react to them (e.g., detecting when two rendered objects has actually made contact). Getting this right for complex assemblies/ many bodies, many simultaneous contacts, tight tolerances has been an ongoing area of investment; see the [devlog](../devlog/) for specific milestones in that work.

Related: [Kinematics](kinematics.md) for how joint/link state is computed before physics acts on it, [Rendering & Viewport](rendering-viewport.md) for how simulation state gets to the screen.
