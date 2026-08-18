# Collision System: A Multi-Phase Upgrade

*July 10, 2026*

## TL;DR

Contact-heavy scenes were lagging. Root cause: collision detection was comparing every body against every other body with no way to skip pairs that couldn't possibly be touching, plus a separate correctness bug where hollow or concave shapes were being treated as solid. Fixed with a phased, additive upgrade across seven fronts rather than a rewrite.

## The Problem

Investigating reported lag during contact-heavy scenes traced the real cost to collision detection's first stage: every category of pair (moving-vs-driven, moving-vs-moving, moving-vs-fixed, sensor checks) was comparing every body against every other body with no spatial shortcut, so the expensive detailed shape-vs-shape checks were running far more often than necessary, cost scaled badly as soon as objects were actually near each other.

A second, unrelated problem surfaced during the redesign: the simplified collision shape used for arbitrary meshes approximates a sealed outer hull, which means a hollow or concave structure (a tunnel, an enclosure) gets treated as solid. Not just a performance issue, a correctness one.

## The Upgrade

Rather than a full rewrite, the fix landed as a phased,

1. **A spatial acceleration structure**, so detailed shape checks only run on pairs that are actually near each other — this alone took "far from anything" down to close to zero collision work, and was the highest-priority, highest-leverage change of the seven.
2. **Proper decomposition of concave/hollow shapes** into multiple simpler solid pieces, so hollow interiors stopped producing false contacts.
3. **Targeted optimization of the detailed shape-vs-shape checks themselves**, sequenced *after* the spatial structure landed so it was optimizing a much smaller, already-filtered set of checks rather than a brute-force one.
4. **Generalizing "the ground"** from one hardcoded plane to any surface a body can rest on, based on contact angle.
5. **Skipping collision work entirely** for pairs that are both already at rest.
6. **Decoupling the visual frame rate from the physics step**, so rendering smoothness no longer depends on collision cost.

Every phase was scoped to be additive, preserving existing node behavior and file formats, none of the steps required changes to how a node or a saved project behaves from the outside.

## Result

Scenes that were previously paying collision cost proportional to every body against every other body now only pay for pairs that are actually near each other, and the two correctness gaps (unstable resting contact, hollow shapes reading as solid) are closed alongside the performance work rather than left for a separate pass.

---

Related: [Physics & Collision Pipeline](../architecture/physics-collision-pipeline.md#collision-handling).
