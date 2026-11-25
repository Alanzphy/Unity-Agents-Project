# vision/

YOLO person detection exported to OpenVINO, plus `VisionNode` / `SharedModel` (ADR 0004).
Rules: [AGENTS.md](AGENTS.md).

## Model files

- Training/export uses `ultralytics`; runtime uses `openvino` only.
- `.pt` weights and datasets are not committed. TODO: document where to download them.
- Exported OpenVINO IR (`.xml` + `.bin`) is committed through Git LFS.
