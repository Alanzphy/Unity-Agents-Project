# Setup

> Status: **Agreed** (exact Unity patch pending: Eliab fills it in after installing)

## Versions (everyone uses exactly these)

| Tool | Version | Why |
|---|---|---|
| Unity | **6 LTS — `6000.x.yfz` (TODO: exact patch)** | ADR 0002 |
| Python | **3.12** (`.python-version`) | ADR 0005 |
| Python deps | exact pins in `requirements.txt` | ADR 0005 |
| Git LFS | any recent | binaries (ADR 0006) |

## 1. Clone (everyone)

```bash
git lfs install            # once per machine (install Git LFS first: https://git-lfs.com)
git clone https://github.com/Alanzphy/Unity-Agents-Project.git
cd Unity-Agents-Project
```

## 2. Python environment (agents, vision, and whoever runs integration)

With [uv](https://docs.astral.sh/uv/) (recommended, installs Python 3.12 for you):

```bash
uv venv --python 3.12 .venv
uv pip install -r requirements.txt
```

Or with plain Python 3.12:

```bash
python3.12 -m venv .venv                 # Windows: py -3.12 -m venv .venv
source .venv/bin/activate                # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

`ultralytics` pulls `torch`, which is a large download (GBs on Linux with CUDA wheels).

## 3. Git hooks (everyone, once per clone)

```bash
uv tool install pre-commit     # or: pip install pre-commit
pre-commit install             # installs pre-commit + commit-msg hooks
pre-commit run --all-files     # optional: check everything now
```

## 4. Unity (unity team, and whoever runs integration)

1. Install Unity Hub and the exact Unity version above (Hub shows a warning if a project uses a
   different one).
2. Unity Hub -> **Add project from disk** -> select the `unity/` folder (not the repo root).
3. **First time only (Eliab):** create the project with Unity Hub somewhere else, then copy its
   `Assets/`, `Packages/` and `ProjectSettings/` into `unity/`. Do not copy `Library/`.
4. Check *Project Settings -> Editor*: **Asset Serialization = Force Text**, **Version Control =
   Visible Meta Files** (Unity 6 defaults).

### Smart merge for scenes and prefabs (unity team, once per machine)

Add to your global git config (`git config --global --edit`), with your Unity version in the path:

```ini
[merge]
    tool = unityyamlmerge
[mergetool "unityyamlmerge"]
    trustExitCode = false
    cmd = '<UnityYAMLMerge path>' merge -p "$BASE" "$REMOTE" "$LOCAL" "$MERGED"
```

| OS | `UnityYAMLMerge` path |
|---|---|
| Windows | `C:\Program Files\Unity\Hub\Editor\<version>\Editor\Data\Tools\UnityYAMLMerge.exe` |
| macOS | `/Applications/Unity/Hub/Editor/<version>/Unity.app/Contents/Tools/UnityYAMLMerge` |
| Linux | `~/Unity/Hub/Editor/<version>/Editor/Data/Tools/UnityYAMLMerge` |

On a scene/prefab conflict run `git mergetool`.

## 5. Run the full simulation (integration)

> Pending: `server.py` and the Unity bridge are not implemented yet.

1. Terminal: activate `.venv`, run `python server.py` (listens on `127.0.0.1:8000`).
2. Unity: open `unity/`, open the main scene, press Play.
