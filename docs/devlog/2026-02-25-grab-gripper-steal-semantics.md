# Grab & Gripper: Steal Semantics

*February 25, 2026*

## TL;DR

Grip detection was firing too early — the moment a gripper's proximity sensor touched an object, before the fingers had actually closed around it. Rebuilt grab detection as a physics-response problem instead of a proximity one, and release as its mirror image.

## The Problem

The original grab system welded an object to the gripper as soon as a sensor around the end-effector detected it nearby; before the fingers had actually closed on it. Fixing this properly meant rethinking what "gripped" actually means.

## The Approach

Rather than watching where the fingers touch, the system watches the target object's motion *relative to the gripper*: once a disturbed object's relative velocity settles to near zero, it's being held. A guard against objects that were already stationary before the fingers arrived prevents false positives on things that were never actually disturbed.

Release works as the mirror of this: physics is restored to the object immediately when release is commanded, and release is only confirmed once the object has moved clearly past its original grip position by a margin; confirming the fingers have actually opened past it, not just that the command was sent.

The reference frame used for these checks evolved from something that had to be manually wired per gripper into something the node discovers automatically from whichever nearby moving parts look like fingers, so the system works with any gripper design without extra setup.

## The Result

The final design splits into a background service that owns the shared physics/registry logic (never placed directly by a user) and a user-facing `GripperNode` where the finger parts are picked from a dropdown, with presets for common gripper types (parallel-jaw, three-finger, suction, magnetic) that auto-configure sensible detection thresholds. Only one connection is strictly required to use it.

Because grip state lives in one shared registry rather than per-gripper, one gripper can pick up an object already held by another; hand-off between grippers works without any extra wiring. The team calls this the **"steal"** behavior informally: a second gripper can claim an object mid-hold rather than requiring an explicit release-then-re-grab cycle.

---

Related: [Node Types](../architecture/node-types.md) for `GripperNode`, [Physics & Collision Pipeline](../architecture/physics-collision-pipeline.md).
