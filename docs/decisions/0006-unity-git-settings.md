# 0006 — Unity text serialization, meta files, LFS, smart merge

- Status: Accepted
- Date: 2026-11-24

## Context

Unity scenes and prefabs conflict easily; binary assets bloat Git history.

## Decision

- Asset Serialization = **Force Text**, Version Control = **Visible Meta Files** (Unity 6 defaults).
- Binaries (textures, models, audio, ML weights) through **Git LFS** (`.gitattributes`).
- **UnityYAMLMerge** configured per machine as merge tool (`docs/setup.md`).
- One owner per scene; others work in prefabs (`docs/conventions.md`).

## Consequences

- Readable diffs for scenes/prefabs and fewer conflicts.
- GitHub limits free LFS storage and bandwidth: avoid committing unused Asset Store packages.
