# Kinematics

Every articulated body in Actuasim; a robot arm, a leg on a mobile robot, a mechanism imported from CAD needs the same two questions answered: given the joints' current positions, where is everything in space (**forward kinematics**), and given where something needs to be, what should the joints be set to (**inverse kinematics**)? Most robots are simple open chains, where that's a well-posed problem. Some mechanisms aren't, a piston-driven assembly or a coupled linkage forms a **closed loop**, which needs a different approach layered on top.

## Forward Kinematics (FK)

Forward kinematics computes the pose of every link from a known set of joint values, by composing each joint's transform relative to its parent down the chain root to end-effector (or root to foot, for a leg). This runs continuously, every simulation step, for any articulated body in a project: a `RobotArmNode`, each leg of a `MobileRobotNode`, an `AssemblyNode`. It's what makes a joint value on a node correspond to an actual position in the 3D viewport, and it's the baseline every other kinematic feature builds on physics, rendering, and any downstream logic reading a link's transform all depend on FK being current.

## Inverse Kinematics (IK)

Inverse kinematics is the reverse problem: given a target pose for an end-effector (or a foot, for a leg), solve for the joint values that get there. This is what's actually running when a project drives a robot by *where it should be* rather than by *what each joint should be set to* a `RobotArmNode` following a `WaypointsNode`, or a `Transform` port target instead of individually commanded joints.

Most non-trivial arms and legs don't have a closed-form IK solution, so this is solved numerically/iteratively rather than with a single formula. For mechanisms with enough joints or complexity that solving IK every step would be expensive to do inline, that solving can run as its own process the graph node talks to at a fixed update rate, keeping the heavier math off the graph's own execution — see the kinematics node in the [example graph](../../README.md#in-action) for what that looks like in practice.

## Mobile Robots: Per-Leg Kinematics

A legged `MobileRobotNode` doesn't solve one big kinematic problem for the whole robot — each leg is its own independent open kinematic chain, with its own FK/IK. Placing a foot at a target point on the ground is an IK problem local to that one leg; the robot's overall gait is a matter of coordinating several of these per-leg solves together (see the hexapod example in the [README](../../README.md#in-action)), not a single whole-body solve.

## Closed-Loop Mechanisms

Robot description formats like URDF assume a strict **tree** structure: every link has exactly one parent, and the kinematic chain flows one direction from root to leaves. Real mechanical assemblies frequently don't look like that.

### The Problem

CAD assemblies are graphs, not trees; parts can connect to multiple other parts, and constraints between them form closed loops. A piston mechanism is a simple example: a linear actuator drives a rod, which drives a crank, which is also constrained by the housing it rotates against a loop, not a chain.

```
CAD assembly (graph):          URDF (tree what's actually loadable):
                                
  handle1 ←→ pusher ←→ handle2         base_link
     ↑                   ↑              /   |   \
     └──── piston_rod ────┘         link1 link2 link3
```

URDF has no native way to express the closed-loop version. Importing an assembly exported directly from CAD tends to produce structurally invalid URDF duplicate link definitions, or links with more than one parent joint.

### The Approach: Break the Loop, Then Mimic It Back

Actuasim resolves this in two steps:

1. **Choose a primary kinematic chain.** One actuator is designated the *master*, the thing actually being commanded (e.g., the piston's prismatic joint).
2. **Express everything else as mimic joints.** Every other joint that was part of the closed loop becomes a *slave*, whose position is computed directly from the master:

   ```
   slave_position = multiplier × master_position + offset
   ```

A crank that rotates 180° over a 0.1m piston stroke, for instance, is expressed as a mimic joint with a multiplier derived from that ratio rather than as an independently-actuated joint.

### What This Buys You

- **One control point.** Downstream logic (a PLC Controller node, ROS2, whatever's driving the graph) only needs to command the master joint, the rest of the assembly follows automatically.
- **A valid tree underneath a graph-shaped mechanism.** The kinematic solver still operates on a tree; the loop is resolved analytically via the mimic relationships rather than requiring general-purpose constraint solving at runtime.
- **Auto-suggested relationships.** Structural hints that matching joint-naming patterns, identical axis orientations, similar joint types are used to propose likely mimic relationships, rather than requiring every multiplier to be derived by hand.

### Limitations

This is a kinematic solution, not a dynamic constraint solver: mimic joints propagate *position*, not force. Each slave has exactly one master, and cyclic master-slave dependencies aren't supported the loop has to be broken at exactly one point, by design.

See [`docs/devlog/2026-02-15-closed-loop-kinematics-placo.md`](../devlog/2026-02-15-closed-loop-kinematics-placo.md) for the story behind the current closed-loop solver.

---

Related: [Node Types](node-types.md) for `RobotArmNode`, `MobileRobotNode`, and `AssemblyNode`, [Physics & Collision Pipeline](physics-collision-pipeline.md) for how kinematic state feeds into simulation, [Data Formats](data-formats.md) for where mimic-joint metadata is stored.
