# AGENTS.md

Rules for AI coding agents **and** humans working in this repository. Every agent tool loads this
file (Claude Code via `CLAUDE.md`, Gemini via `.gemini/settings.json`, Copilot via
`.github/copilot-instructions.md`; Codex/Cursor/Copilot agent read it natively). Folders have
their own `AGENTS.md` with component-specific rules; the closest one also applies.

## Project

Digital twin of a MAV (drone) swarm that searches for a described person (Intel challenge).
Unity renders the world and cameras; Python (AgentPy + YOLO/OpenVINO) is the swarm brain.
Read `docs/architecture.md` before making cross-component changes.

## Repo map and ownership

| Path | What | Owner |
|---|---|---|
| `unity/` | Unity 6 LTS project: environment, people, drones, cameras, HTTP client | Eliab, Fabián |
| `agents/` | AgentPy model: CentralControl, MAV agents, consensus | Mirka |
| `vision/` | YOLO person detection, OpenVINO export + inference, `VisionNode` | Ari, Alan |
| `docs/` | Architecture, API contract, data model, conventions, decisions (ADRs) | Everyone |
| `server.py` | Single Python entry point: serves the API for Unity | agents + vision |

## Hard rules

1. **Stay in the task's folder.** Do not edit another component's folder unless the task says so;
   if you must, say it explicitly so the owner reviews it.
2. **The message contract is `docs/api.md`.** Any change to the Unity <-> Python messages updates
   `docs/api.md` first and both sides in the same PR. Never invent fields that are not there.
3. **Decisions are fixed by ADRs** (`docs/decisions/`). Do not change the Unity version, Python
   version (3.12), dependency pins, tick model or vision architecture without a new ADR.
4. **Dependencies:** edit `requirements.in`, regenerate `requirements.txt` with the command in its
   header. Never `pip install` something and leave it undeclared.
5. **Never commit:** `unity/Library/`, `Temp/`, `Logs/`, `UserSettings/`, virtualenvs, model
   weights (`*.pt`), datasets, or binaries outside Git LFS.
6. **Git:** branch `<area>/<short-topic>` (areas: `unity`, `agents`, `vision`, `api`, `docs`,
   `ci`, `repo`); Conventional Commits (`feat(vision): ...`); PRs only, never push to `main`.
   No AI attribution in commits.
7. **Generated artifacts are in English** (code, comments, docs, commit messages).
8. **Do not guess.** If the contract, an ADR or the docs don't answer something, ask instead of
   assuming.

## Before finishing a change

- `pre-commit run --all-files` passes (ruff for Python; whitespace/YAML/JSON/large-file checks).
- You ran or tested what you changed; say how.
- Docs updated if behavior, setup or a decision changed.

## More detail

- Setup and how to run: `docs/setup.md`
- Code style and naming: `docs/conventions.md`
- Messages and endpoints: `docs/api.md`
- Entities: `docs/data-model.md`
