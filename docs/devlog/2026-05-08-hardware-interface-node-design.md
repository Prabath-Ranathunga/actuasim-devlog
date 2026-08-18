# Designing the Hardware Interface Node

*May 8, 2026*

## TL;DR

An architecture session for the node that bridges the graph to real microcontroller boards over USB — protocol choice, board support, and a pin-allocation system that prevents conflicting wiring before it happens.

## Protocol & Boards

Evaluated an existing hobbyist communication protocol first but decided against it, it was missing support for some communication modes the project needs, and maintaining two different firmware paths for the sake of compatibility wasn't worth the added complexity. Settled on one small custom firmware and protocol used across every supported board, with the node checking for the expected handshake on connect and prompting a reflash if a board doesn't respond correctly which also gives firmware-version and board-type checking for free.

Board support spans the common microcontroller families mentioned in the [README](../../README.md), each with a full pin-capability map (not just a pin count) feeding a pin registry: it tracks each pin's allocation state and drives the configuration UI, so already-used pins disappear from selection, shared-bus connections automatically reserve their fixed pins, and conflicting configurations get blocked before they can be created.

## Channels & Buses

Simple digital/analog/PWM channels and structured bus connections (I2C/SPI/UART) got separated into distinct configuration flows, since bus setup needs to account for board-specific limitations that a simple channel doesn't. For bus data specifically, decided against parsing device-specific protocols inside the hardware node itself, it passes raw bytes through, and a separate decoder node downstream handles interpreting them for a specific device, keeping the hardware node itself a plain protocol gateway rather than something that needs updating for every new sensor.

Also worked out a concrete testing plan for validating every communication mode between two real boards before considering the design done.

---

Related: [Node Types](../architecture/node-types.md) for `HardwareInterfaceNode`.
