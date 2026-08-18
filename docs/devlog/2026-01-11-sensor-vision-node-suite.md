# Sensor & Vision Node Suite

*January 11, 2026*

## TL;DR

Built out the camera and LiDAR sensor family for Mobile Robot Node, then spent the second half of the session sketching a broader node taxonomy and the shape of a future PLC-style automation layer.

## Sensor Nodes

Added a configurable Camera Node (field of view, resolution, several output formats RGB, depth, combined RGB-D, grayscale, infrared) that attaches to any mount point rather than being baked into the robot node itself, plus a Camera Viewer node for inline live-feed display. Followed with a 2D LiDAR node (configurable scan arc, noise/intensity options) and a 3D LiDAR node with presets modeled on common real sensor families, covering multi-beam vertical distribution and point-cloud downsampling/filtering informed by real-world sensor data rates to plan ahead for buffer and export needs. Rounded out with a system-level camera node for external webcam capture, aimed at vision workflows outside the simulated scene itself.

## Planning: Node Taxonomy & Automation

The second half was planning-only, no code: sketched a general node taxonomy (sources/sinks/storage under one umbrella; math/logic/conversion/timing/AI under another) and scoped PLC-style automation as its own category; a node-based control-logic system that can both simulate a machine's behavior and, eventually, export the same logic to run on real PLC hardware.

Landed on a consolidation-first approach: rather than one node per sensor or actuator brand, single configurable nodes cover a whole family through a property (matching the project's general preference for fewer, more composable node types). The planned set covers digital/analog I/O, control-logic primitives, and a handful of simulation-bridging nodes that don't exist on real PLCs but are needed to connect logic to physics, things like a virtual limit switch driven by collision detection, a force/torque reading, and a part spawner for production-line-style scenes. The goal: build and test control logic entirely in simulation, then generate the same logic for deployment once a physical machine exists.

---

Related: [Node Types](../architecture/node-types.md), [LiDAR Sensor System](../architecture/lidar-sensor-system.md).
