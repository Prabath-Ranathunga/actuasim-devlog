# PLC Automation: From Logic Export to Assembly Actuation

*February 14, 2026*

## TL;DR

Built out the PLC-facing side of automation: exporting/importing control logic to real PLC platforms, I/O expander nodes, and a clean split between "what moves" (Assembly Node) and "how it's driven" (an Actuator node).

## Logic Export & I/O

Added export/import for the PLC Controller node so logic can round-trip between the simulator and real PLC platforms, with the source format auto-detected on import. Kept the conversion itself deliberately basic; a starting point rather than a certified converter, since production logic needs manual review regardless of how it got there. Also built I/O Expander nodes that auto-detect which PLC module they're attached to and dynamically expose individual bit-level ports from a single connection, collapsing cleanly if disconnected.

## Assembly & Actuation

The first pass at driving imported mechanisms baked a specific actuator type directly into one combined node. Course-corrected into a cleaner split: an Assembly Node that loads an imported mechanism, lists every detected movable joint, and lets the user tick only the ones that should actually be driven, everything else stays static, paired with a separate Actuator Node that self-configures from the joint information the assembly exposes rather than needing manual setup.

Built a Linear Actuator node on top of that supporting several actuation types, configurable travel limits, realistic acceleration-limited motion instead of instant snapping, and limit-switch-style outputs computed from position rather than requiring separate physical sensors.

## Stepping Back

Also scoped the broader automation node library and confirmed the guiding philosophy: logic testing and visualization, not detailed component-level simulation of every sensor and motor. Landed on a tiered roadmap manual inputs, PLC logic, I/O expansion, actuators, assembly/CAD visualization, then displays and logging as the shape of what to build next.

---

Related: [Kinematics](../architecture/kinematics.md), [Node Types](../architecture/node-types.md).
