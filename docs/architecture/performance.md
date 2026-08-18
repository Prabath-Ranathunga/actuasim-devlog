# Performance

Actuasim is built with a specific constraint in mind: it should run comfortably on an engineer's normal laptop, not require a workstation-class machine to be usable. That constraint shapes decisions across the physics, sensor, and rendering subsystems, not just one optimization pass.

## CPU/RAM as a First-Class Metric, Not an Afterthought

The [workspace status bar](workspace-ui.md#the-main-workspace) shows live CPU and RAM usage for the application at all times. On an empty, freshly-opened project, that baseline is:

```
CPU: 0.6%
RAM: 2.0% (658 MB)
```

Making that number ambient and always visible is itself a design choice: if resource usage only shows up when someone goes looking for it, it's easy for it to creep upward unnoticed. Keeping it on-screen by default keeps it something the app is implicitly accountable to, project after project. Number was design to color to `Amber` or `Red` depend on usage and if usage over 90% will notify the user.

For scale, a real, non-trivial scene; a six-legged robot with a kinematics solver, teleoperation input, and a live 3D view all running at once reads `CPU: 5.3%` / `RAM: 4.6% (1491 MB)`. Going from an empty project to a fully simulated, actively-driven robot is roughly a 2.5× RAM increase and single-digit CPU usage, not a jump into territory that needs a beefier machine. (Note: for Physics engines like collision will load on startup and that will give peaks of usages and settle down after loading)

## Designing for CPU/RAM, Not GPU Horsepower

The default assumption is a modest CPU and modest memory, rather than a high-end discrete GPU:

- **Simulation fidelity is decoupled from rendering fidelity** (see [Rendering & Viewport](rendering-viewport.md)) frame rate limits, shadows, and anti-aliasing are independent of physics accuracy, so visual quality can be turned down without touching simulation correctness, and vice versa.
- **Work moves to the GPU only where it earns its keep.** Pushing rendering and calculation to `CPU` rather than running on `GPU` is a targeted decision  to make this application available for systems that depend on on borad GPU or that doesn't have higher end GPU. 
- **Only what's wired to run costs anything.** `Run`/`Stop` nodes gate which parts of a graph are actively stepping (see [Physics & Collision Pipeline](physics-collision-pipeline.md#running-the-graph)), so isolating one subsystem to debug it doesn't require the rest of a heavy project to keep running alongside it.

## What This Trades Off

Optimizing for approachable hardware means large, dense scenes (many bodies, many simultaneous LiDAR sensors, very high solver iteration counts) will eventually be limited by CPU/RAM headroom rather than GPU compute. That's an accepted tradeoff for a tool meant to be picked up on whatever machine an engineer already has, rather than one that assumes a specific hardware budget.

( There's pending update on automatically detect `CPU`/`CPU` and then allocate resources accordingly )