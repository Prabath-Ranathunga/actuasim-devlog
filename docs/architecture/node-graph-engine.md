# Node Graph Engine

The node graph is the backbone of Actuasim every behavior in a project, It is expressed as nodes connected by typed ports.

## Nodes

A node is a self-contained unit with three parts:

- **Inputs** : data or signals coming in
- **Outputs** : data or signals produced
- **Properties** : configurable parameters set directly on the node

Nodes are grouped into categories, laid out as their own labeled sections on the toolbar so the catalog reads as "what kind of thing is this" at a glance:

| Category | Purpose | Real examples |
|---|---|---|
| General | Core utility, display, and graph-structuring nodes | Camera Viewer, Constant Value, Display Text, Graph, Record/Replay, Time |
| Function | Calculations, logic, signal shaping, and execution control | Arithmetic, Comparator, PID, Script, Run, Stop |
| World | Coordinate systems and static scene setup | World Frame, Offset Frame |
| Robot | Robot bodies, actuators, sensors, and physical objects | Camera, Drone, Gripper, Joint Slider, LiDAR, Mobile Robot, Robot Arm |
| Automation | Control-system and industrial-automation building blocks | PLC Controller, Conveyor, Discrete/Continuous Actuator, Proximity Sensor, Assembly |
| Hardware | Bridges to real, physical devices | Bus Connection, CAN Bus, Hardware Interface, IMU |

This is a large set of 54 node types across these six categories as of this writing and it keeps growing as new integrations and robot primitives get added. See [Node Types](node-types.md) for the full list, including what a node's documentation actually looks like inside the app.

## Ports and Typing

Connections are only valid between compatible port types. Ports aren't just "number" or "string", they're grouped into families that map onto concepts directly: spatial/transform data, kinematic state, physics quantities, and raw signal types, alongside media like long dictionaries . See [Port Types](port-types.md) for the full breakdown, including exactly what each port type's underlying data shape is.

Rules that keep the graph well-formed:

1. Only ports of compatible types can connect.
2. Data flows output → input.
3. One output can fan out to many inputs.
4. One input accepts exactly one connection; creating a new one replaces the old.

This typing is what lets very different domains kinematics, vision, control logic share a single graph without silently passing garbage between them.

## Connections and Update Rate

A connection isn't just a wire that moves a value once, it's a live link that's re-evaluated as the graph runs. A few things shape how "fast" a given connection effectively is:

- **The simulation step rate.** Physics- and kinematics-facing ports (joint state, transforms, contacts) are only as fresh as the last simulation step. see [Physics & Collision Pipeline](physics-collision-pipeline.md#running-the-graph) for how that step is driven.
- **The node's own update behavior.** Some nodes (sensors, PID controllers, timers) are naturally periodic; others (a constant, a static frame) only need to emit once and hold.
- **Fan-out doesn't cost extra wiring overhead.** Because one output can drive many inputs, sharing a value across the graph doesn't mean re-computing it per consumer.

Slower-moving data (a display label, a logged constant) and fast-moving data (live joint state during a control loop) coexist on the same graph without one starving the other, because each connection is only re-evaluated as often as its producing node actually updates. (Note: Normal port connections are capped to 60Hz to keep the resources and performance steady, some engines like collision solvers run at 120Hz on simulation space but its outputs through ports capped to 60Hz)

## Why a Graph Instead of a Script

Some projects tend to be assembled from a handful of recurring concerns (get sensor data → run some logic → command an actuator) wired together in project-specific ways. A graph makes those wiring decisions visible and editable directly, rather than buried in code that has to be re-read to understand data flow. It also means the same visual representation serves as both the implementation and the documentation of how a given robot behaves.

Related: [Node Types](node-types.md) and [Port Types](port-types.md) for the full reference, [Workspace & UI](workspace-ui.md) for where all this lives on screen, [Physics & Collision Pipeline](physics-collision-pipeline.md) for what happens once the graph starts running, [Data Formats](data-formats.md) for how a graph is persisted.
