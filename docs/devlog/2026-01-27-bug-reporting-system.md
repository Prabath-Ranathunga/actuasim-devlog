# Bug Reports Going Missing

*January 27, 2026*

## TL;DR

Errors and crashes weren't reliably reaching the team's alert channel. Root cause: the UI framework was silently swallowing exceptions before they ever reached the app's normal error hook.

## The Investigation

The real gap: exceptions raised inside the UI event loop, a button handler, a signal callback, were being silently absorbed by the framework before Python's top-level error hook ever saw them, so failures inside almost any interactive action were dying with no trace. Background-thread errors weren't hooked at all either. On top of that, reports were sent synchronously with no retry, so a dropped connection meant a lost report, and there was no de-duplication, so one repeating error could spam the channel with the same message over and over.

## The Fix

Rebuilt the reporting path around a queued sender with retry and backoff, a cooldown window per distinct error signature to kill duplicate spam, and the actual fix for the main gap hooking directly into the UI framework's own exception path, plus a separate hook for background-thread errors, so nothing gets silently dropped before it can be reported.

Next: confirm framework-level errors are actually landing in the channel in the live app before trusting the pipeline.

---

Related: [Performance](../architecture/performance.md).
