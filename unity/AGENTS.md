# unity/ — Unity 6 LTS project

Applies in addition to the root `AGENTS.md`.

- This folder is the Unity project root (`Assets/`, `Packages/`, `ProjectSettings/`). Open it with
  the exact Unity version in `docs/setup.md`; never upgrade the project without an ADR.
- Our content lives under `Assets/_Project/` (`Scenes/`, `Prefabs/`, `Scripts/`, `Materials/`,
  `Models/`). Third-party assets go to `Assets/ThirdParty/`.
- **Scene ownership:** each scene has one owner (listed in `docs/conventions.md`). Others work in
  prefabs and ask the owner to place them. Never edit a scene you don't own.
- Always commit the `.meta` file together with its asset. Never commit `Library/`, `Temp/`,
  `Logs/`, `UserSettings/`.
- C#: namespace `MavSwarm.*`, one public type per file, file name = class name, PascalCase public
  members, `[SerializeField] private` instead of public fields.
- Communication with Python only through the client that implements `docs/api.md`. Positions are
  Unity world coordinates in meters (left-handed, Y up).
- Unity is the clock (ADR 0003): it sends `POST /step` every tick and applies the reply.
