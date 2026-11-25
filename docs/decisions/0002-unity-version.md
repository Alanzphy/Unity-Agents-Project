# 0002 — Latest Unity 6 LTS, exact patch for everyone

- Status: Accepted (exact patch pending)
- Date: 2026-11-24

## Context

Opening a Unity project with a different editor version upgrades and rewrites many project files,
producing huge diffs and broken scenes or packages.

## Decision

Use the latest **Unity 6 LTS** available in Unity Hub. Everyone installs the **same exact patch**
(e.g. `6000.x.yfz`), recorded in `docs/setup.md` and in `unity/ProjectSettings/ProjectVersion.txt`.

## Consequences

- Upgrading the editor requires a new ADR and is done by one person in its own PR.
- TODO: Eliab records the exact patch after installing.

## Rejected alternatives

- **Older LTS (2022.3):** no reason to start a new project on an older line.
