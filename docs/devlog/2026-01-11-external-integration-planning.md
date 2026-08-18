# Scoping External Integrations: Live Code, USB, Multi-ROS

*January 11, 2026*

## TL;DR

An architecture-only session mapping out three integration paths before writing any of them: driving nodes from live external code, capturing real USB devices, and connecting to multiple independent ROS networks at once.

## What Got Decided

**Live external code** : Settled on a client-server model; the app runs a background server that an external code editor connects to, so triggering a node opens the mapped source file, runs it with the node's live inputs/outputs, and streams results back into the graph.

**System inputs** : For cameras, microphones, and generic USB devices, settled on background capture threads feeding data in through the normal thread-safe signal path rather than polling on the main thread, with hot-plug detection so a device showing up mid-session gets picked up automatically.

**Multi-ROS** : The bigger decision: rather than a single ROS connection, the app needs a bridge manager supporting several independent ROS networks simultaneously, each identified separately. This calls for new node types, a connection node, publisher, subscriber, service call, and a routing node for moving data between two separate ROS networks, each tracking its own connection state (disconnected/connecting/connected/error) visibly on the node itself.

## Why It Matters

The multi-ROS decision reframes the app from "a simulator with a ROS bridge bolted on" into a visual multi-robot orchestration layer; subscribing and publishing across independent ROS systems and transforming data between them entirely through the graph, rather than being limited to one robot's ROS stack at a time.

No code was written this session, the point was locking in the shape of the problem before building any of it.

---

Related: [Node Types](../architecture/node-types.md), [Node Graph Engine](../architecture/node-graph-engine.md).
