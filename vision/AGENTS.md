# vision/ — YOLO + OpenVINO

Applies in addition to the root `AGENTS.md`.

- ADR 0004: one `VisionNode` per MAV, all sharing **one** compiled OpenVINO model. Each node has
  its own infer request, confidence threshold and detection history. Never compile the model per node.
- Frames arrive as 640x480 JPEG (base64) at 2 Hz per drone (`docs/api.md`).
- Exported models (OpenVINO IR `.xml` + `.bin`) go through Git LFS; `.pt` weights and datasets are
  not committed (document where to download them in `vision/README.md`).
- `ultralytics` is used for training/export; runtime inference uses `openvino` only.
- Type hints on public functions; tests with `pytest` under `vision/tests/`.
