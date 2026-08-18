# Making CAD Import Not Freeze the App

*March 8, 2026*

## TL;DR

Large CAD file imports were freezing the UI entirely. Fixed by moving the import work off the main thread, then spent the rest of the session speeding up everything downstream of it — and closed out by designing the project's new all-in-one save format.

## The Freeze

The immediate problem: importing a large CAD file blocked the entire interface for as long as the import took — several seconds on bigger files, completely frozen. Fixed by pushing the import and processing work onto a background thread pool while the main thread keeps the UI responsive, since the underlying import step releases its lock during the heavy native work, letting things genuinely run in parallel. Added request deduplication at the same time — if several nodes ask for the same file at the same moment (common when reopening a project), only one import actually runs and every caller shares the result.

## Everything Downstream

From there, the session moved through general performance work: replacing per-element loops in the mesh-processing math with vectorized array operations, processing multiple solids within one file concurrently instead of one at a time, and precompiling pattern-matching logic used during import instead of re-deriving it every time. Also improved visual quality — welding vertices across part seams to eliminate visible gaps, and smoother shading on curved surfaces — both absorbed into the existing background work at no extra UI cost.

The on-disk cache format was switched to a more compact, faster-to-load representation — a large speedup on bigger meshes — and cache keys were redesigned to be based on file content rather than an absolute file path, so cached data stays valid across different machines and folder locations.

## Designing `.acs`

Closed out the session designing the project's new all-in-one save format: a single bundle containing the workspace itself, a cache of already-processed meshes, and referenced asset files together — so reopening a project means reading one file back, not reprocessing everything from source geometry again (see [Data Formats](../architecture/data-formats.md)).

---

Related: [Data Formats](../architecture/data-formats.md), [Performance](../architecture/performance.md).
