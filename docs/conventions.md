# Conventions

> Status: **Agreed**

## Git

- `main` is protected (ruleset "main: PR + CI (fast)"): no direct pushes, force pushes or
  deletion; changes only through PRs whose CI checks are green. **No approval is required**: the
  author merges their own PR once CI passes.
- `.github/CODEOWNERS` automatically requests a review from the folder owner. Reviewing is
  encouraged, mandatory only by team agreement: changes to `docs/api.md`, `requirements.*` or
  `.github/` should wait for one teammate's approval.
- Merging: squash only (the PR title becomes the commit message, so it must be a Conventional
  Commit). Enable auto-merge on your PR (`gh pr merge --auto --squash`) and it merges itself when
  CI passes; the branch is deleted after merge.
- Branches: `<area>/<short-topic>` — areas: `unity`, `agents`, `vision`, `api`, `docs`, `ci`, `repo`.
  Example: `vision/openvino-export`.
- Commits: [Conventional Commits](https://www.conventionalcommits.org/):
  `<type>(<scope>): <summary>` — types `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`,
  `perf`; scope = area. Example: `feat(agents): add zone partition`. Checked by the `commit-msg`
  hook and by CI on the PR title.
- Small PRs (aim for < 400 changed lines). One topic per PR.
- No AI attribution lines in commits.
- Use GitHub Issues for tasks and the Project board to track them; reference issues in PRs
  (`Closes #12`).

## Language

Code, comments, docs, commit messages and PRs in **English**. Team chat in any language.

## Python (`agents/`, `vision/`, `server.py`)

- Python 3.12. Formatter and linter: `ruff` (config in `pyproject.toml`), run by pre-commit.
- `snake_case` functions/variables, `PascalCase` classes, `UPPER_CASE` constants.
- Type hints on public functions. Docstrings only where the intent isn't obvious.
- Tests with `pytest` in `<package>/tests/`.
- Dependencies only via `requirements.in` -> regenerate `requirements.txt` (command in its header).

## Unity / C# (`unity/`)

- Our content under `Assets/_Project/` (`Scenes/`, `Prefabs/`, `Scripts/`, `Materials/`,
  `Models/`); third-party under `Assets/ThirdParty/`.
- Namespace `MavSwarm.<Area>` (e.g. `MavSwarm.Drones`, `MavSwarm.Bridge`).
- One public type per file; file name = class name; `PascalCase` for types, methods and public
  members; `camelCase` for private fields; `[SerializeField] private` instead of public fields.
- Prefer prefabs; keep scenes thin.

### Scene ownership (proposal: Eliab and Fabián confirm)

| Scene | Owner |
|---|---|
| `Main.unity` (search environment) | Eliab |
| `Integration.unity` (bridge tests) | Fabián |

Others never edit a scene they don't own: build prefabs and ask the owner to place them.

## Documentation

- Every cross-component change updates `docs/` in the same PR.
- A change to a fixed decision needs a new ADR in `docs/decisions/` (next number).
- Diagrams in Mermaid inside Markdown (render on GitHub, diffable).
