# Documentation

| Doc | Content | Status |
|---|---|---|
| [architecture.md](architecture.md) | Components, how they connect, tick model | Draft |
| [agents.md](agents.md) | Agents in the system and their relationships (deliverable) | Draft |
| [class-diagram.md](class-diagram.md) | Class diagram (deliverable) | Draft |
| [protocol.md](protocol.md) | Interaction protocol diagrams (deliverable) | Draft |
| [api.md](api.md) | Unity <-> Python message contract | Draft |
| [data-model.md](data-model.md) | Shared entities and fields | Draft |
| [conventions.md](conventions.md) | Branches, commits, code style, Unity rules | Agreed |
| [setup.md](setup.md) | Versions, install and run | Agreed |
| [decisions/](decisions/) | Architecture Decision Records (ADRs) | — |

**Draft** = proposal written from the challenge text and the decisions taken; the team reviews and
edits it in a PR. **Agreed** = accepted by the team; changes need a PR (and an ADR when they
change a decision).

## ADRs

| # | Decision |
|---|---|
| [0001](decisions/0001-monorepo.md) | One monorepo, one folder per component |
| [0002](decisions/0002-unity-version.md) | Unity 6.3 LTS (`6000.3.29f1`), exact patch for everyone |
| [0003](decisions/0003-unity-owns-time.md) | Unity owns simulation time; Python is called per tick |
| [0004](decisions/0004-vision-node-per-mav.md) | One VisionNode per MAV sharing one compiled model |
| [0005](decisions/0005-python-version.md) | Python 3.12, one venv, exact pins |
| [0006](decisions/0006-unity-git-settings.md) | Unity text serialization, meta files, LFS, smart merge |
| [0007](decisions/0007-fastapi.md) | FastAPI + uvicorn for the Python API |

New decision: copy the structure of an existing ADR, next number, short and in English.
