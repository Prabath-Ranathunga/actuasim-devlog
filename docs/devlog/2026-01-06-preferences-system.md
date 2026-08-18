# A Preferences System for ~90 Loose Settings

*January 6, 2026*

## TL;DR

Built a proper preferences dialog so the dozens of tunable constants scattered through the app's config could be edited without touching code.

## What Happened

The starting problem: roughly 90 loose settings; grid sizes, camera defaults, theme colors, cache behavior, that could only be changed by editing source directly. Built a tabbed settings dialog grouped by subsystem (Workspace / Node & Edge / Theme / Simulation / CAD & Objects / Other) rather than one long flat form, matching a broader preference for grouping related settings over dumping everything into one place.

Each setting's editor control is inferred automatically from its value's type, a number gets a spinbox, a boolean gets a checkbox, a color gets a picker, so adding a new setting later is a one-line addition rather than hand-building a UI control for it. A parallel snapshot of every default value backs a "Reset All" option.

Closed out the session with a full dark-theme pass across the whole dialog, including forcing the native window title bar into dark mode too, so the dialog doesn't flash a bright white frame against the rest of the UI when it opens.

---

Related: [Workspace & UI](../architecture/workspace-ui.md), [Rendering & Viewport](../architecture/rendering-viewport.md).
