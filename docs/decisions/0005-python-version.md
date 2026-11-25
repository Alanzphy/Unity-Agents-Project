# 0005 — Python 3.12, one venv, exact pins

- Status: Accepted
- Date: 2026-11-24

## Context

Verified on PyPI (2026-11-24): `agentpy` 0.1.5 is the last release (2021-12-21) and does not cap
its dependencies; `openvino` 2026.4.1 needs Python >= 3.10; `numpy` 2.5.5 needs >= 3.12;
`pandas` 3.1.1 needs >= 3.11. `server.py` loads AgentPy and the vision model in one process.

## Decision

Python **3.12**, one shared virtualenv, direct deps in `requirements.in`, cross-platform lock in
`requirements.txt` generated with
`uv pip compile requirements.in --universal --python-version 3.12 -o requirements.txt`.

## Evidence

Smoke test (2026-11-24, Python 3.12.16): agentpy 0.1.5 + numpy 2.5.5 + pandas 3.1.1 +
openvino 2026.4.1 — a 3-agent `ap.Model` ran `setup/step/update` manually and via `run()` with no
errors and no warnings.

## Consequences

- If AgentPy breaks later, Python 3.12 still allows the fallback `numpy<2`, `pandas<3`
  (wheels exist for 3.12), recorded in a new ADR.
- Changing a pin = edit `requirements.in`, regenerate the lock, PR.

## Rejected alternatives

- **Python 3.13+:** no numpy 1.x wheels, so no fallback if AgentPy breaks with numpy 2.
- **One venv per folder:** the server runs both in one process, so they must agree anyway.
