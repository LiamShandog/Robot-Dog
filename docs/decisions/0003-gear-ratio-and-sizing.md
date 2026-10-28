# 0003 · Gear ratio 8:1 with a ~100 Kv motor
2026-10-28 · Status: accepted (pending validation in sim)

## Context

First-pass hand sizing of the knee actuator, done before choosing a motor.

**Assumptions**

| Parameter | Value |
|---|---|
| Robot mass | 7 kg |
| Thigh / shank | 0.2 m / 0.2 m |
| Standing height (hip to foot) | 0.28 m, foot directly below hip |
| Trot load | 2 feet down, peak 3× their static share (~103 N per foot) |
| Gearbox efficiency | 90% |
| Battery | 24 V (6S) |
| Kt estimate | Kt ≈ 8.27 / Kv (Nm/A) |

**Sizing chain**

- Force per foot, standing: 7 × 9.81 / 4 ≈ 17.2 N
- Knee moment arm: knee sits 0.14 m below the hip, so horizontal offset = √(0.2² − 0.14²) ≈ 0.143 m
- Hip torque ≈ 0 in this pose (foot force passes through the hip), so the knee sets the actuator size
- Knee torque, standing: ≈ 2.4 Nm
- Knee torque, trot peak: ≈ 14.4 Nm, **above the original 12 Nm peak target**
- Speed at 6:1, 100 Kv, 24 V: 2400 rpm no-load at the motor → ~42 rad/s no-load at the joint → ~21 rad/s usable (half of no-load) → foot speed ~5.9 m/s from the hip, ~4.2 m/s from the knee. A trot at 0.5 m/s needs roughly 1-1.5 m/s of foot speed, so there is a ~3-4× speed surplus.

## Options considered

| Option | Effect | Cost |
|---|---|---|
| Bigger motor at 6:1 | Meets 14.4 Nm directly | More weight and price |
| **Higher ratio (8:1)** | Spends speed surplus on torque | Reflected inertia 64× vs 36×; smaller sun gear |
| Shorter legs (0.15 m) | Less torque at the knee | Less stride and ground clearance; leg and sim redesign |
| 2× load factor instead of 3× | Lower requirement on paper | Risk of undersizing if 3× is realistic |

**Comparison, 6:1 vs 8:1 at 100 Kv**

| | 6:1 | 8:1 |
|---|---|---|
| Motor torque for 14.4 Nm knee | 2.7 Nm | 2.0 Nm |
| Phase current at 100 Kv | ~33 A | ~24 A |
| Usable joint speed | ~21 rad/s | ~16 rad/s |
| Foot speed (hip / knee) | 5.9 / 4.2 m/s | 4.4 / 3.1 m/s |
| Reflected inertia factor | 36× | 64× |

## Decision

- Gear ratio **8:1**, single-stage planetary (ring teeth = 7 × sun teeth).
- Motor around **100 Kv** (or lower), at 24 V.
- Motor requirement: **≥ 2.0 Nm peak**, aiming for ~2.5 Nm to leave margin.
- Keep 0.2 m legs for now. Shorter legs are the fallback.

## Why

Want to go for a simple, cheaper motor. Can always reduce leg size later on, to compensate for too little torque.

## What would make me revisit this

- Gearbox design issues, such as lack of strength from PLA.
- The PLA sun gear fails or wears out in the G1 load test (8:1 makes the sun gear smaller and it carries full motor torque).
- Sim shows the speed margin is too thin for push recovery or fast steps.
- Measured Kt or motor torque comes in well below the estimate.
- Fallback order: shorter legs (0.15 m) first, then a bigger motor.
