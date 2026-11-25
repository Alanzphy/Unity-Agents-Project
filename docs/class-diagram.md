# Class diagram

> Status: **Draft** (deliverable). The vision node is **owned by each MAV** (composition 1:1)
> and all nodes **share one compiled OpenVINO model** (ADR 0004).

## Python side (agents/ + vision/)

```mermaid
classDiagram
    direction LR

    class SwarmModel {
        +list~MAV~ mavs
        +CentralControl control
        +int tick
        +setup()
        +step()
        +apply_unity_state(state: StepRequest)
        +collect_reply() StepResponse
    }

    class CentralControl {
        +MissionDescription mission
        +list~Zone~ zones
        +list~Finding~ findings
        +MissionStatus status
        +start_mission(desc: MissionDescription)
        +partition_area(n: int) list~Zone~
        +reassign_zone(mav_id: str)
        +register_finding(f: Finding)
        +check_termination() bool
    }

    class MAV {
        +str id
        +Vec3 position
        +float battery
        +Zone zone
        +MAVState state
        +VisionNode vision
        +list~Vote~ pending_votes
        +step()
        +plan_patrol() list~Vec3~
        +avoid_neighbors(neighbors: list~MAV~) Vec3
        +request_confirmation(d: Detection)
        +handle_message(msg: Message)
        +vote(d: Detection) Vote
        +next_command() Command
    }

    class VisionNode {
        +str mav_id
        +float threshold
        +list~Detection~ history
        -InferRequest request
        +infer(frame: Image, pose: CameraPose) list~Detection~
        +matches_description(crop: Image) float
        +estimate_world_position(bbox, pose) Vec3
    }

    class SharedModel {
        <<singleton>>
        -CompiledModel compiled
        +load(path: str, device: str)
        +create_infer_request() InferRequest
    }

    class Message {
        +str performative
        +str sender
        +str receiver
        +dict content
    }

    class Zone {
        +str id
        +Bounds bounds
        +float coverage
    }

    class Detection {
        +str mav_id
        +BBox bbox
        +float confidence
        +float match_score
        +Vec3 world_position
    }

    class Vote {
        +str voter_id
        +bool yes
        +float confidence
    }

    class Finding {
        +Vec3 position
        +list~Vote~ votes
        +int tick
    }

    SwarmModel "1" *-- "1" CentralControl : coordinates with
    SwarmModel "1" *-- "3..*" MAV : contains
    CentralControl "1" --> "3..*" MAV : assigns zones
    CentralControl "1" o-- "*" Finding : registers
    CentralControl "1" *-- "3..*" Zone : partitions
    MAV "1" *-- "1" VisionNode : owns (not shared)
    VisionNode "3..*" --> "1" SharedModel : uses
    MAV "1" --> "1" Zone : patrols
    MAV ..> Message : sends / receives
    MAV "*" -- "*" MAV : consensus (cfp, propose, vote)
    VisionNode ..> Detection : produces
    MAV ..> Vote : casts
    Finding "1" o-- "2..*" Vote : confirmed by
```

## Unity side (unity/)

```mermaid
classDiagram
    direction LR

    class SimulationManager {
        +float tickInterval
        +List~DroneController~ drones
        +StartMission(desc: string)
        +Tick()
    }

    class BridgeClient {
        +string serverUrl
        +PostMission(req) MissionResponse
        +PostStep(req) StepResponse
    }

    class DroneController {
        +string id
        +Vector3 targetWaypoint
        +float speed
        +ApplyCommand(cmd: Command)
        +GetState() DroneState
    }

    class DroneCamera {
        +int width
        +int height
        +float fov
        +CaptureJpeg() byte[]
        +GetPose() CameraPose
    }

    class PersonSpawner {
        +int count
        +Spawn(target: PersonSpec, distractors: int)
    }

    class Person {
        +string id
        +bool isTarget
        +Color jacketColor
    }

    class HUD {
        +ShowDetections(list)
        +ShowEvents(list)
        +ShowCoverage(float)
    }

    SimulationManager "1" --> "1" BridgeClient : sends each tick
    SimulationManager "1" *-- "3..*" DroneController : controls
    DroneController "1" *-- "1" DroneCamera : has
    SimulationManager "1" --> "1" PersonSpawner : sets up scene
    PersonSpawner "1" --> "*" Person : spawns
    SimulationManager "1" --> "1" HUD : updates
```

The two diagrams meet only through `docs/api.md` (`BridgeClient` <-> `server.py`).
