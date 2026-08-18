# Contact-Driven Locomotion

*June 21, 2026*

## TL;DR

Mobile Robot Node doesn't assume how a robot moves. Locomotion emerges from which contacts wheels or feet, derived straight from the robot's own joint structure; are actually touching the ground on a given tick, rather than from a scripted gait animation or a hand-picked "locomotion type."

## The Idea

Rather than building separate movement logic per robot category (wheeled vs. legged vs. hybrid), the node derives locomotion structure directly from the imported robot description: every continuously-rotating joint becomes a driven wheel contact, and every leaf link that isn't a wheel becomes a foot contact. Body motion and turning aren't commanded directly; they emerge from averaging whichever contacts are currently grounded and how they're loaded. The same underlying system ends up handling a wheeled platform and a legged one without needing a locomotion-type switch anywhere.

## Gait as an Optional Layer

For legged robots, an optional gait generator can drive the underlying contacts toward a commanded body velocity, with presets for common gait patterns. It's a layer on top of the contact model, not a replacement for it, direct per-joint control is still available for anyone who wants to drive individual joints themselves instead.

## Stability Is Computed, Not Assumed

The body itself is treated as a floating rigid body suspended on whichever legs are currently in stance; the floor only positions the feet, not the body directly. Stability comes from checking the center of mass against the support polygon formed by the grounded contacts each tick a robot naturally topples once its center of mass moves outside that polygon, rather than being scripted to fall over. The same live contact data (grounded or not, clearance from the floor, load share per contact) is what answers "why isn't this robot moving" or "why did it tip", the state is always derived from what's actually touching the ground, not from an assumption about what should be.

---

Related: [Kinematics](../architecture/kinematics.md), [Node Types](../architecture/node-types.md) for `MobileRobotNode`.
