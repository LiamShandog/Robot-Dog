# 0003 · Gear ratio 9:1 with a ~300 Kv outrunner (revision 2)
2026-10-29 · Status: accepted (motor-specific numbers TBD; pending validation in sim)

Revision 2 replaces the 7 kg / 12-DOF sizing from revision 1 (kept below under [History](#history)), after the move to 8 DOF in [0005](0005-8-dof-planar-legs.md).

## Context

**Assumptions**

| Parameter | Value |
|---|---|
| Robot | 8 DOF, planar legs (hip flexion + knee), see [0005](0005-8-dof-planar-legs.md) |
| Robot mass target | ~2.5-3 kg |
| Thigh / shank | 0.15 m / 0.15 m |
| Standing stance | hip to foot ≈ 0.21 m, foot directly below hip (knee moment arm ≈ 0.107 m) |
| Trot load | 2 feet down, peak 3× their static share |
| Gearbox efficiency | 90% |
| Battery | 24 V (6S) |
| Kt estimate | Kt ≈ 8.27 / Kv (Nm/A) |
| Motor budget | No hard per-motor cap; all motors (8 + 1-2 spares) together must stay under $1,000 CAD |
| Reference design | ODRI Solo-8: T-Motor Antigravity 4004 300KV, 9:1 two-stage timing belt, 2.5 Nm at 12 A, ~150 g per actuator, robot ~2 kg |

**Sizing chain** (same method as revision 1, run in reverse: from motor to maximum robot mass)

- Knee torque available = motor torque × 9 × 0.9
- Max foot force = knee torque ÷ 0.107 m
- Max robot mass = (max foot force × 2 feet) ÷ (3 × 9.81)

**Results so far**

| Motor | Kv | Motor torque used | Knee torque | Max robot mass (3× trot) |
|---|---|---|---|---|
| RCTimer 5010 | 260 | 0.64 Nm (Kt ≈ 0.032 Nm/A × 20 A) | 5.15 Nm | ~3.27 kg |
| T-Motor MN4004 (Solo-8) | 300 | TBD | TBD | TBD |
| Motor from the aerospace bay | ~300 | TBD | TBD | TBD |

## Options considered

| Option | Effect | Cost |
|---|---|---|
| 6:1 or 8:1 (revision 1) | Less reflected inertia, more speed | Not enough torque from a ~300 Kv motor this size |
| **9:1** | Matches Solo-8, which is proven at this scale; 3:1 × 3:1 in two stages, or ~9:1 single-stage planetary | Reflected inertia 81×; single-stage planetary near its practical limit (~10:1), small sun gear |
| 10:1+ | More torque margin | Backdrivability and speed drop; single-stage planetary no longer practical |

Planetary vs timing belt is a separate decision (transmission record, TBD).

## Decision

- Gear ratio **9:1**.
- Motor class: **~300 Kv outrunner**, 24 V. Specific motor chosen in 0001.
- Legs **0.15 m / 0.15 m**.
- A motor is acceptable if the sizing chain above gives a max robot mass at or above the mass target with 3× trot load.

## Why

The design has been shifted to center around motor feasibility and cost. Motors required for our initial design are too expensive. I can get 300 Kv outrunner BLDC motors for a relatively fair price.

## What would make me revisit this

- The chosen motor's max robot mass comes in below the mass target.
- Sim shows the speed margin is too thin, or the 3× trot assumption is wrong in either direction.
- The PLA transmission can't carry the torque at 9:1.
- Measured Kt comes in well below the estimate.

---

## History

### Revision 1 · 2026-10-28 · 8:1 with a ~100 Kv motor (superseded)

Sized for a 7 kg, 12-DOF robot with 0.2 m / 0.2 m legs, 0.28 m stance, 3× trot:

- Force per foot, standing: 7 × 9.81 / 4 ≈ 17.2 N
- Knee moment arm: √(0.2² − 0.14²) ≈ 0.143 m; hip torque ≈ 0 with the foot under the hip
- Knee torque: ≈ 2.4 Nm standing, ≈ 14.4 Nm trot peak (above the original 12 Nm target)

| | 6:1 | 8:1 (chosen) |
|---|---|---|
| Motor torque for 14.4 Nm knee | 2.7 Nm | 2.0 Nm |
| Phase current at 100 Kv | ~33 A | ~24 A |
| Usable joint speed | ~21 rad/s | ~16 rad/s |
| Foot speed (hip / knee) | 5.9 / 4.2 m/s | 4.4 / 3.1 m/s |
| Reflected inertia factor | 36× | 64× |

Decision: 8:1, ~100 Kv, motor ≥ 2.0 Nm peak.

Why (Liam): "Want to go for a simple, cheaper motor. Can always reduce leg size later on, to compensate for too little torque."

Superseded because ~100 Kv motors were too expensive for 13 actuators. Downsizing the robot, then moving to 8 DOF ([0005](0005-8-dof-planar-legs.md)), changed every input.
