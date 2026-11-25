# API contract (Unity <-> Python)

> Status: **Draft**. This file is the single source of truth for messages between `unity/` and
> `server.py`. Change it first, then both sides, in the same PR.

## Transport

- HTTP/1.1 + JSON, `server.py` listens on `http://127.0.0.1:8000` (configurable).
- Unity is the client and the clock (ADR 0003): one `POST /step` per tick (default 0.5 s).
- Frames: JPEG 640x480, base64 in JSON, one per drone per tick (ADR 0004).
- Server: FastAPI + uvicorn (ADR 0007). Pydantic models mirror this file; interactive docs at
  `http://127.0.0.1:8000/docs`.

## Conventions

- Coordinates: Unity world space, meters, left-handed, **Y up**. Vectors are `[x, y, z]`.
- Rotations: quaternions `[x, y, z, w]`.
- Ids: strings, drones `mav-1`, `mav-2`, ...; people `person-1`, ...
- Time: `sim_time` in seconds since mission start; `tick` integer starting at 0.
- Unknown fields must be ignored (forward compatible). Missing or invalid fields -> HTTP 422.

## `POST /mission`

Starts a mission and returns the zone assignment.

Request:

```json
{
  "description": "Person with an orange jacket in the north quadrant",
  "area": { "min": [-50, 0, -50], "max": [50, 30, 50] },
  "drones": [
    { "id": "mav-1", "position": [-40, 10, -40] },
    { "id": "mav-2", "position": [0, 10, -40] },
    { "id": "mav-3", "position": [40, 10, -40] }
  ],
  "tick_interval": 0.5,
  "time_limit": 300
}
```

Response `200`:

```json
{
  "mission_id": "m-001",
  "zones": [
    { "id": "zone-1", "drone_id": "mav-1", "min": [-50, 0, 0], "max": [0, 30, 50] },
    { "id": "zone-2", "drone_id": "mav-2", "min": [0, 0, 0], "max": [50, 30, 50] },
    { "id": "zone-3", "drone_id": "mav-3", "min": [-50, 0, -50], "max": [50, 30, 0] }
  ]
}
```

## `POST /step`

Request (Unity -> Python):

```json
{
  "mission_id": "m-001",
  "tick": 42,
  "sim_time": 21.0,
  "drones": [
    {
      "id": "mav-1",
      "position": [-12.5, 10.0, 30.2],
      "velocity": [1.2, 0.0, 0.4],
      "battery": 0.87,
      "collided": false,
      "camera": {
        "width": 640,
        "height": 480,
        "fov_deg": 60,
        "position": [-12.5, 9.8, 30.2],
        "rotation": [0.38, 0.0, 0.0, 0.92],
        "image_jpeg_b64": "<base64>"
      }
    }
  ]
}
```

Response (Python -> Unity):

```json
{
  "tick": 42,
  "commands": [
    { "drone_id": "mav-1", "type": "goto", "target": [-10.0, 10.0, 35.0], "speed": 4.0 },
    { "drone_id": "mav-3", "type": "hover" }
  ],
  "detections": [
    {
      "drone_id": "mav-2",
      "bbox": [312, 140, 380, 300],
      "confidence": 0.62,
      "match_score": 0.71,
      "world_position": [14.1, 0.0, 41.7]
    }
  ],
  "events": [
    { "type": "confirm_request", "from": "mav-2", "position": [14.1, 0.0, 41.7] },
    { "type": "vote", "from": "mav-3", "yes": true, "confidence": 0.81 },
    { "type": "target_confirmed", "position": [14.1, 0.0, 41.7], "votes": 2 }
  ],
  "mission": { "status": "confirming", "coverage": 0.42, "collisions": 0 }
}
```

| Field | Values |
|---|---|
| `commands[].type` | `goto` (needs `target`, `speed`), `hover`, `return_home` |
| `events[].type` | `confirm_request`, `propose`, `refuse`, `vote`, `target_confirmed`, `false_positive`, `mission_end` |
| `mission.status` | `searching`, `confirming`, `found`, `not_found`, `timeout` |
| `bbox` | `[x1, y1, x2, y2]` pixels, origin top-left |

`events` exist so Unity can **visualize** the protocol (lines between drones, popups). Unity never
decides on them.

## World position of a detection (proposal)

Python estimates `world_position` by casting a ray from the camera pose through the bbox
bottom-center and intersecting the ground plane `y = 0` (flat terrain assumption). If the terrain
is not flat, Unity could add a depth value per detection later (contract change).

## Errors

| Code | When |
|---|---|
| 422 | Invalid JSON or missing/invalid field (FastAPI validation error body) |
| 409 | `mission_id` does not match the running mission |
| 500 | Server error; Unity keeps the last commands and retries next tick |
