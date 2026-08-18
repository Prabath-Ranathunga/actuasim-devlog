# Simulation Window & Scene Hierarchy

*December 30, 2025*

## TL;DR

Built out the full simulation window UI; viewport controls, a scene-tree panel, an orientation gizmo and made the 3D view derive its visibility directly from the node graph rather than keeping its own separate state.

## What Happened

Built the simulation window from individual pieces up: a floating toolbar for camera controls (orbit/pan/zoom/fit) and grid/link/CAD visibility toggles, a collapsible scene-tree panel showing the frame/object hierarchy with per-item visibility toggles, and an orientation gizmo that got a proper fix for its 3D-to-2D projection and depth-based fading (the original math was broken). Also pulled scattered UI tuning values camera limits, colors, tessellation quality into one central place, and added caching so re-importing the same CAD source doesn't redo the work.

## The Key Decision

Rather than trusting frame parent-child relationships as static data, frame visibility is now derived *live* from the node graph: a frame (and everything under it) only renders if there's an actual active connection tracing all the way back to a world reference. Break a link anywhere in that chain and everything downstream frames, their geometry, their sub-links disappears from both the 3D view and the scene-tree panel at the same moment, because both read from the same underlying "connected to world" state instead of maintaining independent visibility logic. This keeps the 3D view an honest, always in sync mirror of the graph rather than a separately maintained picture of it.

---

Related: [Simulation Space](../architecture/simulation-space.md), [Node Graph Engine](../architecture/node-graph-engine.md).
