# 0004 — One VisionNode per MAV sharing one compiled model

- Status: Accepted
- Date: 2026-11-24

## Context

The challenge asks whether the vision node belongs to each MAV or is shared, and requires
confirming findings by swarm consensus. The model weights are identical for every drone.

## Decision

Each MAV owns one `VisionNode` (composition 1:1) with its own OpenVINO infer request, confidence
threshold, detection history and vote. All nodes use **one** model compiled once
(`SharedModel`). Frames: 640x480 JPEG at 2 Hz per drone (one per tick), tunable.

## Consequences

- Matches the decentralized Edge AI scenario: each drone perceives and votes independently.
- Memory cost of one model, not N.
- Bandwidth on localhost: ~6 frames/s for 3 drones (~300 KB/s), negligible.

## Rejected alternatives

- **One shared central vision service:** simpler, but centralized (single point of failure) and
  makes the consensus less meaningful.
- **N independently compiled models:** same behavior, N times the memory.
