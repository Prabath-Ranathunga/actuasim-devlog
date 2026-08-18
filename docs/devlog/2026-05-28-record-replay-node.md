# Record & Replay: Building `.acsbag`

*May 28, 2026*

## TL;DR

Built the Record/Replay node; one node, two modes and designed the `.acsbag` session-recording format after ruling out plain text formats as unworkable at real data volumes.

## The Node

In record mode, the node samples whatever ports it's connected to on every tick and streams them to disk. In replay mode, the same channels play back out through matching output ports, with a playhead that supports play/pause/seek/speed control in real time.

## The Format

Ruled out a plain text-based format early, a modest LiDAR recording at a normal scan rate would balloon into gigabytes uncompressed, completely impractical. Landed on a compressed, chunked binary container under the `.acsbag` extension (see [Data Formats](../architecture/data-formats.md)) that supports random access without loading the whole file, handles everything from single values to point clouds in the same container, and builds a coarse index while recording so playback can jump to any timestamp without scanning from the start.

Export gates the available formats by what's actually in a given recording, simple scalar/vector recordings can export to common lightweight formats, while anything containing point-cloud data is limited to a robotics-standard recording format, since there's no reasonable plain-text representation for a scan with thousands of points per frame. All exports run in the background so the UI stays responsive throughout.

Replay interpolates sensibly per data type, smooth interpolation for continuous values, proper rotation interpolation for transforms, and hold-last-value for discrete on/off channels.

---

Related: [Data Formats](../architecture/data-formats.md).
