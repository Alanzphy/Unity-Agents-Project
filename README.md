# Unity Agents Project — MAV swarm search (Intel challenge)

Digital twin of a swarm of at least 3 MAVs (drones) that split and patrol a 3D area, avoid
collisions, and detect a described person (e.g. "person with an orange jacket in the north
quadrant") with YOLO optimized by OpenVINO, confirming findings by swarm consensus.

Tecnológico de Monterrey, Campus Chihuahua — *Modelación de sistemas multiagentes con gráficas
computacionales*, group 300.

## Components

| Folder | What | Owner |
|---|---|---|
| [`unity/`](unity/) | Unity 6 LTS: environment, people, drones, cameras, visualization | Eliab, Fabián |
| [`agents/`](agents/) | AgentPy: central control, MAV agents, consensus | Mirka |
| [`vision/`](vision/) | YOLO person detection, OpenVINO export and inference | Ari, Alan |
| [`docs/`](docs/) | Architecture, API contract, diagrams, conventions, decisions | Everyone |

```
Unity (cameras, physics) --POST /step every 0.5 s--> server.py (AgentPy + VisionNodes)
       ^                                                         |
       +------------------ commands, detections, events ---------+
```

## Quick start

See [docs/setup.md](docs/setup.md). Short version:

```bash
git lfs install && git clone https://github.com/Alanzphy/Unity-Agents-Project.git
cd Unity-Agents-Project
uv venv --python 3.12 .venv && uv pip install -r requirements.txt
pre-commit install
```

Unity team: open the `unity/` folder in Unity Hub with the exact version in `docs/setup.md`.

## Working together

- Rules for humans and AI agents: [AGENTS.md](AGENTS.md).
- Conventions (branches, commits, code style): [docs/conventions.md](docs/conventions.md).
- Changes go through PRs to `main`; each folder is reviewed by its owner.
- Tasks live in GitHub Issues and the Project board.
