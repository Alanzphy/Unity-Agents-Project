# 0007 — FastAPI + uvicorn for the Python API

- Status: Accepted
- Date: 2026-11-24

## Context

ADR 0003 fixed HTTP + JSON between Unity and `server.py` (`POST /mission`, `POST /step`), but
left the server framework open.

## Decision

Use **FastAPI** served by **uvicorn**. Request/response bodies are Pydantic models that mirror
`docs/api.md` one to one.

## Consequences

- Validation of incoming JSON for free: a missing or wrong field returns an error instead of
  failing deep inside the model (`api.md` errors table).
- Interactive docs at `http://127.0.0.1:8000/docs` to try `/mission` and `/step` without Unity.
- Pinned in `requirements.txt` (fastapi 0.162.2, uvicorn 0.54.0, pydantic 2.14.3); import check
  passed together with agentpy/numpy on Python 3.12 (2026-11-24).
- Note: FastAPI returns **422** for validation errors (not 400); `api.md` reflects that.

## Rejected alternatives

- **Flask:** familiar, but no built-in validation or schema docs; we would hand-write the checks.
