# 0002 · Sim and training stack
Date: 2026-10-29 · Status: accepted

## Context

Sub-project A (sim model) and H (RL training pipeline) both need a sim engine. It has two jobs in this project:

1. **Sizing/validation** (Phase 0-1): rough-size the actuator from simulated peak torque/speed, check the robot can stand and walk under PD control, before ordering parts.
2. **RL training** (H, gated at G5): train a walking + push-recovery policy with PPO, domain randomization, and a measured actuator model from sysid (E), then deploy it to the real robot.

Constraints:

| Parameter | Value |
|---|---|
| GPU | NVIDIA RTX 4060 Max-Q, 8 GB VRAM (laptop, Ada architecture) |
| Robot | 8-DOF, planar legs, no vision/perception in scope |
| Pace | ~15 h/week, solo |
| Deploy stack | Custom CAN bus + direct 50 Hz policy inference (no ROS2 in the plan as of this record) |

## Options considered

| Option | Pros | Cons |
|---|---|---|
| **Isaac Lab** (PhysX) | Most published quadruped RL work (legged_gym/rsl_rl origins) used Isaac Gym/Lab — more reference configs to crib from; GPU-parallelized training | Heavier install (Omniverse stack); more VRAM per parallel env, so fewer parallel envs on 8 GB; its main differentiator (photoreal rendering, synthetic vision) is unused since vision is out of scope; more driver/version sensitivity |
| **MuJoCo / MJX** | Contact solver well regarded for legged locomotion (push recovery, foot-ground contact) — a project success criterion; MJX gives GPU-parallelized training on less VRAM per env than PhysX; lightweight pip install; same engine can cover Job 1 (sizing) and Job 2 (training); already named as the fallback in Risks if training compute gets tight | Newer MJX RL ecosystem — fewer existing reference reward/env configs to copy than Isaac Gym/Lab, more has to be designed from scratch |
| **Gazebo** | Native ROS2 integration (`ros2_control`, sensor plugins, topics) | No native GPU-parallelized RL training, no legged_gym/rsl_rl-equivalent ecosystem; weaker legged-contact dynamics than MuJoCo; its value only attaches if the deploy stack is ROS2-based, which isn't currently the plan. Not a Job 1/Job 2 candidate — at most a later integration-test tool for sub-project I if ROS2 gets adopted |

## Decision

**MuJoCo, with MJX for GPU-parallelized RL training.**

## Why

No vision/perception in scope, so Isaac Lab's main differentiator doesn't apply here. Push recovery and ground contact are explicit success criteria, and MuJoCo's contact solver is the better-regarded choice for that in the legged-locomotion RL community. It also fits the 8 GB VRAM budget better than PhysX per parallel env, and installs with pip instead of the heavier Omniverse stack. One engine covers both the Phase 0-1 sizing/standing work and the later RL training.

## What would make me revisit this

- MJX's reward/env ecosystem turns out too thin to build on solo within the Phase 0-2 timeline → fall back to Isaac Lab for the reference implementations.
- 8 GB VRAM can't fit enough parallel envs for either engine to train in reasonable wall-clock time → rent a cloud GPU (per Risks table).
- Deploy stack decision moves to ROS2 → revisit whether Gazebo earns a role as an integration-test tool (separate from this training-sim choice).
