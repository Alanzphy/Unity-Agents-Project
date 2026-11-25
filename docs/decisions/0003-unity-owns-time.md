# 0003 — Unity owns simulation time; Python is called per tick

- Status: Accepted
- Date: 2026-11-24

## Context

AgentPy runs in discrete steps (`model.step()`); Unity runs in real time. The cameras and
physics live in Unity, and YOLO needs those frames, so Unity cannot be a passive visualizer.

## Decision

Unity is the clock. Every tick (default 0.5 s) it sends `POST /step` with drone states and one
frame per drone. Python runs inference and exactly one `model.step()`, and replies with commands,
detections and events. Unity keeps simulating with the last commands while waiting.
Transport: HTTP + JSON (`docs/api.md`).

## Consequences

- Fluid visualization; simple request/response integration.
- Not fully reproducible (depends on latency). If reproducibility is needed for experiments, a
  lockstep mode (Unity pauses until the reply) can be added later without changing the contract.
- `model.run()` is never used in the server path.

## Rejected alternatives

- **Lockstep only:** exact and reproducible, but the simulation stutters at inference speed.
- **Python as master, Unity only renders:** Python would need to request frames from Unity and
  duplicate 3D collisions.
