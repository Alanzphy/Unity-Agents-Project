# Data model

> Status: **Draft**. Shared vocabulary for both sides. JSON shapes are in `api.md`; classes in
> `class-diagram.md`. Units: meters, seconds, Unity world space (Y up).

| Entity | Key fields | Owned by | Notes |
|---|---|---|---|
| **Mission** | `mission_id`, `description`, `area`, `time_limit`, `status` | CentralControl | One active mission at a time |
| **Zone** | `id`, `drone_id`, `min`, `max`, `coverage` (0..1) | CentralControl | Axis-aligned box; coverage = visited cells / total cells |
| **Drone (MAV)** | `id`, `position`, `velocity`, `battery`, `state`, `zone_id` | Unity (physics) + MAV agent (decisions) | `state`: `patrolling`, `verifying`, `returning`, `idle` |
| **CameraPose** | `position`, `rotation`, `fov_deg`, `width`, `height` | Unity | Needed to project detections to the world |
| **Frame** | `drone_id`, `tick`, JPEG bytes | Unity -> VisionNode | Not stored by default |
| **Detection** | `drone_id`, `tick`, `bbox`, `confidence`, `match_score`, `world_position` | VisionNode | `confidence` = person; `match_score` = matches description |
| **Vote** | `voter_id`, `yes`, `confidence`, `tick` | MAV | One per voter per candidate |
| **Finding** | `position`, `votes`, `tick`, `status` | CentralControl | `status`: `confirmed`, `false_positive` |
| **Command** | `drone_id`, `type`, `target`, `speed` | MAV -> Unity | See `api.md` |
| **Person** | `id`, `position`, `is_target`, `appearance` | Unity (scenario) | Ground truth, used only for evaluation, never sent to Python |

## Mission log (evaluation)

To report the success metrics, `server.py` appends one line per tick to `runs/<mission_id>.jsonl`
(git-ignored): tick, coverage per zone, collisions, detections, events. Ground truth of the target
position is written by Unity at the end to compare.
