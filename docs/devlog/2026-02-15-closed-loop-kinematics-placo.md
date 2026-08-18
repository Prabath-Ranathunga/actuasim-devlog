# Closed-Loop Kinematics with Placo

*February 15, 2026*

> **Status: draft.** This entry sketches the shape of the work; specifics still need to be filled in.

## TL;DR

Mechanisms imported straight from CAD are graphs, not trees; they contain closed kinematic loops that plain URDF can't represent. [TODO: confirm and describe the role Placo (or whichever kinematics library/approach was actually used) played here, this entry is a placeholder based on the filename alone and needs the real story.]

## The Problem

URDF assumes every link has exactly one parent and the kinematic chain flows one direction, root to leaves. Assemblies exported from CAD tools don't respect that; a piston mechanism, a four-bar linkage, or coupled cranks all form loops by construction, and importing them naively produces structurally invalid URDF (duplicate link names, links with multiple parent joints). See [Kinematics](../architecture/kinematics.md#closed-loop-mechanisms) for the shape of the problem.

## Approach

[TODO: what was actually built; was Placo used, for what part specifically (IK, whole-body kinematics, something else)? How does it relate to the mimic-joint master/slave model described in the architecture doc?]

## Result

[TODO: what mechanisms this unlocked, what got simpler]

## Lessons

[TODO: why this approach over the alternatives considered, what the integration cost, what would be reconsidered]

---

See [Kinematics](../architecture/kinematics.md#closed-loop-mechanisms) for the conceptual architecture this work implements.
