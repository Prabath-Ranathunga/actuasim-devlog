# Data Formats

Actuasim persists project data as a main project file plus a small set of narrower, purpose-built formats for the pieces that make sense sharing independently of a whole project.

| Extension | Contains |
|---|---|
| `.acs` | The whole project; every node, connection, and property in the graph, plus a cache of processed 3D assets and attached scripts |
| `.acsrobot` | A robot/mechanism description; links, joints, master/slave (mimic) relationships, sensor and actuator node bindings |
| `.acsbag` | A recorded session; node graph state and port values over time |
| `.acslidar` | A recorded point-cloud stream from a LiDAR node |

## `.acs`: The Project File

`.acs` is what `File → Save` writes and `File → Open` reads back, the single file a project actually lives in day to day. It bundles:

- **The graph itself** : every node, its properties, and every connection between them.
- **A rendering cache** : 3D meshes and imported assemblies already processed into whatever form the viewport and physics need, rather than raw source assets that would have to be re-imported and re-processed on every open.
- **Attached scripts** : anything a `ScriptNode` (or similar) references, kept with the project instead of as a dangling external file path.

Caching processed assets inside the project file is a deliberate trade: a heavy imported assembly pays its processing cost once, at save time, instead of every time the project is reopened, reopening a project means reading one file back, not rebuilding the scene from source geometry again.

## Why the Other Formats Are Separate From `.acs`

`.acsrobot`, `.acsbag`, and `.acslidar` exist because some data is more useful as its own portable file than as something buried inside one project:

- **`.acsrobot`** describes *what a thing is*, independent of any particular simulation run. It's what gets imported when you drop a robot or mechanism into a project, and it's where closed-loop mechanism metadata (master joints, mimic relationships. see [Kinematics](kinematics.md#closed-loop-mechanisms)) lives.
- **`.acsbag`** describes *what happened* in a specific run; useful for debugging, sharing a repro, or feeding recorded behavior back into the graph for playback.
- **`.acslidar`** isolates sensor capture specifically, since point-cloud data is large and often wanted independently of the rest of a session's state. (e.g., for offline perception-algorithm testing without re-running a simulation).

## Design Intent

`.acs` is what makes reopening your own project fast everything it needs is already inside it. The narrower formats are what make sharing one specific slice of a project clean: a robot description can be versioned and shared without dragging a whole project's rendering cache along with it, and a captured LiDAR sweep can be handed to someone who has no need to open the project it came from at all.

Related: [Node Graph Engine](node-graph-engine.md), [Kinematics](kinematics.md), [LiDAR Sensor System](lidar-sensor-system.md), [Performance](performance.md) for why re-processing costs matter.
