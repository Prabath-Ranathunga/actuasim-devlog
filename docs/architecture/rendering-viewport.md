# Rendering & Viewport

The viewport is where a running simulation actually becomes visible — a real-time 3D view of the world the physics and kinematics subsystems are computing.

## Running Is Part of the Graph

`Run` and `Stop` nodes, wired directly into the graph like any other node, are what start and stop a project's execution (see [Physics & Collision Pipeline](physics-collision-pipeline.md#running-the-graph)). A project can wire up several of these, each one gating a different part of the graph — the [example graph](../../README.md#in-action) has a `Run` node feeding the world/kinematics chain and a separate `Run`/`Stop` pair alongside it. The viewport itself just renders whatever the graph is currently doing. (`Run` and `Stop` also just normal boolean input)

## What's Rendered

- The simulated scene itself : robots, mechanisms, environment geometry
- Camera node output, viewable inline via Camera Viewer nodes
- Live data visualization : plotting node output values, sensor feeds, and robot state over time, so the graph's internal data isn't limited to log output

## State Lives on the Node

Live state is surfaced directly on the nodes producing it; a `Mobile Robot` node, for instance, shows its own linear/angular velocity and status flags (e.g. `RUN`, `GROUNDED`) right on the node card as the graph executes. Reading a robot's state means looking at the node that owns it, in the same graph wired it in.

## Rendering Settings

Rendering options : things like frame rate limit, anti-aliasing, and shadows; are configured through **Preferences**, separately from simulation/physics settings (gravity, time step, solver iterations). They're deliberately different groups of settings: turning shadows or anti-aliasing up or down changes what the scene looks like, not how the physics behaves underneath it.

---

Related: [Node Graph Engine](node-graph-engine.md) for how `Run`/`Stop` nodes fit into the graph, [Node Types](node-types.md) for node-level state reporting.