# agents/ — AgentPy swarm model

Applies in addition to the root `AGENTS.md`.

- Python 3.12, AgentPy 0.1.5 (unmaintained since 2021: rely only on `ap.Model`, `ap.Agent`,
  `ap.AgentList`; avoid `ap.Experiment`/sensitivity analysis).
- The model does **not** run its own loop: `server.py` calls `model.step()` once per `POST /step`
  from Unity (ADR 0003). Never call `model.run()` in the server path.
- Agents communicate through explicit messages (see `docs/protocol.md`), not by reading each
  other's internal state.
- Each `MAV` owns exactly one `VisionNode` (ADR 0004); do not call the vision model directly.
- Type hints on public functions; tests with `pytest` under `agents/tests/`.
