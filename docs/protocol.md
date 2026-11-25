# Interaction protocols

> Status: **Draft** (deliverable: "Diagrama de protocolos de interacción")

An interaction protocol diagram shows **who sends which message to whom, in which order, and
which alternatives exist** (accept/refuse, timeouts). Messages use FIPA-ACL performatives
(`request`, `inform`, `cfp`, `propose`, `accept-proposal`, `reject-proposal`, `refuse`), the usual
vocabulary for multi-agent protocols. Diagrams are Mermaid sequence diagrams (render on GitHub).

## P1. Mission start and zone assignment

```mermaid
sequenceDiagram
    actor Op as Intel operator
    participant U as Unity
    participant CC as CentralControl
    participant M as MAV i (x N)
    Op->>U: description + area + N drones
    U->>CC: request(start_mission) [POST /mission]
    CC->>CC: partition area into N zones
    loop each MAV
        CC->>M: inform(assign_zone, zone_i)
    end
    CC-->>U: inform(zones, initial waypoints)
```

## P2. Simulation tick (every 0.5 s, ADR 0003)

```mermaid
sequenceDiagram
    participant U as Unity
    participant S as server.py
    participant V as VisionNode i
    participant M as MAV i
    U->>S: POST /step (poses, frames, collisions)
    loop each MAV
        S->>V: infer(frame, camera pose)
        V-->>M: inform(detections)
    end
    S->>S: model.step()
    Note over M: patrol / avoid / verify / vote
    S-->>U: commands, detections, events, mission status
    U->>U: apply commands, render
```

## P3. Detection confirmation (swarm consensus)

Contract-Net style: the detecting MAV calls for proposals, the best candidate verifies, and the
finding is confirmed by vote.

```mermaid
sequenceDiagram
    participant A as MAV A (detector)
    participant B as MAV B
    participant C as MAV C
    participant CC as CentralControl
    A->>A: detection with conf >= T_low (0.5)
    alt conf >= T_high (0.9)
        A->>CC: inform(candidate, high confidence)
        Note over A,CC: still needs one peer vote
    end
    A->>B: cfp(verify, position)
    A->>C: cfp(verify, position)
    B-->>A: propose(eta = 4 s)
    C-->>A: refuse(busy verifying)
    A->>B: accept-proposal(verify)
    B->>B: fly to viewpoint, VisionNode B inspects
    B-->>A: inform(vote yes|no, confidence)
    alt yes votes >= quorum (2 incl. A)
        A->>CC: inform(target_confirmed, position, votes)
        CC->>A: inform(mission_end)
        CC->>B: inform(mission_end)
        CC->>C: inform(mission_end)
    else rejected or timeout (10 s)
        A->>A: mark position as false positive
        Note over A,B: both resume patrol
    end
```

## P4. Collision avoidance (local, between neighbors)

```mermaid
sequenceDiagram
    participant A as MAV A
    participant B as MAV B
    Note over A,B: every tick, positions come from Unity
    A->>A: distance to B < safe radius (3 m)?
    alt too close
        A->>A: apply separation vector + altitude offset by id
        B->>B: same rule (symmetric, no negotiation)
    end
    Note over A,B: Unity reports any real collision in /step for metrics
```

## P5. Mission end

| Trigger | Who detects | Message |
|---|---|---|
| Target confirmed | CentralControl (from P3) | `inform(mission_end, success)` to all MAVs |
| All zones covered | CentralControl (from MAV status) | `inform(mission_end, not_found)` |
| Time limit | CentralControl | `inform(mission_end, timeout)` |

## Parameters (initial values, to tune)

| Name | Value | Meaning |
|---|---|---|
| `T_low` | 0.5 | Minimum confidence to start a confirmation |
| `T_high` | 0.9 | High confidence: report a candidate early |
| `quorum` | 2 | Yes votes needed (detector included) |
| `verify_timeout` | 10 s | Max wait for a vote |
| `safe_radius` | 3 m | Separation distance between MAVs |
