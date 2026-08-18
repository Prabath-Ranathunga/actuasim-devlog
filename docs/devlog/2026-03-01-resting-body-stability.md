# Objects That Wouldn't Lie Flat

*March 1, 2026*

## TL;DR

Dynamic objects were freezing in place after landing on a corner or edge instead of rotating to rest flat. Traced to several small, compounding bugs in the resting-contact code.

## The Bugs

The visible symptom was simple: an object landing on an edge should tip over and settle flat, but it was locking in place at an angle instead. Tracing it down turned up a stack of separate, compounding issues; the correcting torque was being skipped on ticks where it should have applied, its sign was backwards (fighting gravity instead of helping the object settle), and the contact point used for the correction was unstable, flipping between two candidate points on alternating simulation steps in a way that produced torques that canceled each other out, so the object visibly wobbled by a fraction of a degree without ever actually settling. A separate bug meant idle ("sleeping") objects never woke up to receive the correction at all.

## The Fix

Fixed all of it together, and generalized the whole resting/settling behavior from "the ground specifically" to "any surface", so an object dropped onto another part now settles the same way it would on the floor. Verified numerically: an object starting tipped over at 45° converges to flat within roughly 150 simulation steps.

---

Related: [Physics & Collision Pipeline](../architecture/physics-collision-pipeline.md).
