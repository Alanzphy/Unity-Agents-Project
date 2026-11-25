# 0001 — One monorepo, one folder per component

- Status: Accepted
- Date: 2026-11-24

## Context

Five people work on three components (Unity, AgentPy, YOLO/OpenVINO) that are not independent:
they exchange messages every simulation tick. The course requires one GitHub repo for code and
documentation, also used for team communication.

## Decision

One repository with top-level folders `unity/`, `agents/`, `vision/`, `docs/`. Each member opens
only their folder (Unity Hub opens `unity/`; editors open `agents/` or `vision/`). Ownership is
enforced with `CODEOWNERS` and PR reviews.

## Consequences

- A message contract change (`docs/api.md`) and both sides land in the same PR.
- Everyone downloads the Unity assets; mitigated with Git LFS (and `git sparse-checkout` if needed).

## Rejected alternatives

- **One repo per component:** contract drift between repos, extra coordination, no single place
  to run the integrated simulation.
