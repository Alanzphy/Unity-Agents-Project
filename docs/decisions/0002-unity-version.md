# 0002 — Unity 6.3 LTS, exact patch for everyone

- Status: Accepted
- Date: 2026-11-24

## Context

Opening a Unity project with a different editor version upgrades and rewrites many project files,
producing huge diffs and broken scenes or packages.

## Decision

Use **Unity 6.3 LTS, patch `6000.3.29f1`** (latest 6.3 LTS patch on 2026-11-25). Everyone
installs this exact patch. It is recorded in `docs/setup.md` and, once the project exists, in
`unity/ProjectSettings/ProjectVersion.txt`, which is the source of truth.

## Consequences

- Upgrading the editor requires a new ADR and is done by one person in its own PR.
- If the project is created with a different 6.3 patch, update this ADR and `docs/setup.md` to
  match `ProjectVersion.txt` in the same PR.

## Rejected alternatives

- **Older LTS (2022.3):** no reason to start a new project on an older line.
