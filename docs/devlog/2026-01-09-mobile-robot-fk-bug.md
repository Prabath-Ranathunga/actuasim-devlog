# A Forward-Kinematics Stub Left Wired to Nothing

*January 9, 2026*

## TL;DR

Mobile Robot Node wasn't rendering anything in the simulation, despite Robot Arm Node working correctly right next to it. Root cause: forward kinematics had been left as an empty placeholder.

## What Happened

Comparing the two node implementations side by side traced the problem to the mobile robot's forward-kinematics step never actually running, it had been left as a stub, so the computed link positions that drive both the 3D view and the frame indicators never got populated. The real forward-kinematics logic (walking the kinematic chain and composing each joint's transform) already existed in the same file, it just wasn't wired into the class where it needed to be called. Also caught a block of wheel/mount detection logic that had been duplicated verbatim during an earlier copy-paste between the two node types.

Fix: port the working forward-kinematics path over from the arm node and call it at the right point during setup.

## Takeaway

Platform nodes (mobile robots) and pure model nodes (arms) share the same forward-kinematics and rendering backbone. That shared logic is a good candidate to consolidate into one common implementation rather than re-deriving it separately per node type going forward.

---

Related: [Kinematics](../architecture/kinematics.md), [Node Types](../architecture/node-types.md).
