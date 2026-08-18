# Robot Arm Node: Getting the Boundary Right

*January 4, 2026*

## TL;DR

First pass at the Robot Arm node tried to do too much; IK solving, trajectory interpolation, and control-mode switching all folded into one node. Caught it early and re-architected around a cleaner split.

## What Happened

Started building the Robot Arm node, the piece that gets an actual robot model into the simulator, the next step after simpler static objects. The first pass followed an instinct that had worked before (consolidating a robot's behavior variants into one node): fold IK solving, trajectory interpolation, and control mode switching directly into the arm node itself.

That over scoped it. A robot arm isn't one behavior with variants; it's shared state that several different, independent controllers need to act on. Re-architected around a clean boundary: the arm node itself just parses the robot's description, holds joint state, and computes forward kinematics every tick nothing else. All the actual control (a joint slider, an IK solver, a trajectory generator, a path planner) moved out into their own separate nodes that write to the arm's joint inputs and read its pose/limits outputs.

## Why It Matters

This keeps the graph composable: swapping a slider node for an IK node for a trajectory player means rewiring one connection, not touching the arm node at all. It's consistent with a broader pattern already used elsewhere; processing stages should stay visible and individually swappable rather than baked together.

Next: build the actual controller nodes, starting with a joint slider since it's the fastest path to a demoable interaction, then FK/IK utility nodes.

---

Related: [Kinematics](../architecture/kinematics.md), [Node Types](../architecture/node-types.md).
