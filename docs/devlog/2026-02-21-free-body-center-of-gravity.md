# Center of Gravity: From a Point Mass to Real Mesh Volume

*February 21, 2026*

## TL;DR

Free-standing objects had no real center of gravity; every object was treated as a point mass at its geometric center, so an off-center hit produced pure sliding instead of any tipping.

## The Problem

The question that surfaced the gap was simple: would dropping something on top of a free-standing object make it tip over? It didn't, it just slid, because there was no concept of mass distribution to make it want to rotate at all.

The underlying constraint is that imported CAD geometry carries no mass data; mass, center of gravity, and inertia live inside the original design tool and don't travel with an exported shape. So for an arbitrary imported part, everything about its physical mass properties has to be derived from its geometry alone.

## The Fix

Replaced the previous bounding-box volume approximation with the true enclosed volume computed directly from the mesh itself, which also gives an accurate volumetric center of gravity as a side effect, a meaningful improvement over a bounding-box center for anything that isn't already a simple box.

Added a manual override (a position input plus a toggle) for the cases where the uniform-density assumption breaks down, a multi-material assembly exported as a single shape, for instance. Also gave resting-contact physics a proper inertia model and angular response, so an object's rotation now responds correctly to an off-center impact instead of just translating.

Wrapped up by writing a full internal engineering reference covering the math behind all of this, kept as a technical document, not a user-facing guide.

---

Related: [Physics & Collision Pipeline](../architecture/physics-collision-pipeline.md).
