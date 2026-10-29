# Robot-Dog: Open-Source Quadruped

An 8-joint quadruped robot (~2-3 kg, two joints per leg, inspired by [ODRI Solo-8](https://github.com/ToolKnox/hawkridge-odri-solo8-open-quadruped-robot)) built from the motor up: custom quasi-direct-drive actuators, custom motor driver and FOC firmware, a walking policy trained with reinforcement learning in simulation and run on the real robot. Everything gets open-sourced.

**Started:** Oct 28, 2026 · **Planned finish:** Apr 25, 2027 · **Pace:** about 15 hours/week over 26 weeks

> If my pace changes, I shift the dates in [Timeline](#timeline), not the gates. A phase is only finished when its gate is met.

---

## Status

| Gate | Target week | Target date | What has to be true | Status |
|---|---|---|---|---|
| G1 | 7 | Dec 13, 2026 | Actuator v1 holds closed-loop torque control; bandwidth and torque constant measured | Not started |
| G2 | 12 | Jan 17, 2027 | Single leg tracks stance and swing trajectories; sim matches measured response. **Go/no-go for building 8 actuators** | Not started |
| G3 | 15 | Feb 7, 2027 | Custom driver v2 matches v1 performance in the leg test | Not started |
| G4 | 19 | Mar 7, 2027 | Full robot stands under PD control in a harness; e-stop cuts motor power | Not started |
| G5 | 24 | Apr 11, 2027 | Robot walks, tracks velocity commands, recovers from a push | Not started |
| G6 | 26 | Apr 25, 2027 | Repo, build guide and write-up published | Not started |

Status values: `Not started` · `In progress` · `Met (date, link to gate review)` · `Missed (link to decision)`

---

## Goal and success criteria

The project is done when **all** of these are true, each backed by a measurement or video in this repo:

- [ ] Walks on flat ground and follows forward/backward and limited turning velocity commands (target 0.5 m/s forward). No sideways walking: legs are planar, see [0005](docs/decisions/0005-8-dof-planar-legs.md)
- [ ] Recovers from a moderate push without falling, shown on video
- [ ] Control stack runs at fixed rates: policy at 50 Hz, joint loop at ≥1 kHz, firmware current loop at ≥10 kHz
- [ ] Runs untethered on battery for ≥20 minutes of mixed standing and walking
- [ ] Someone else can rebuild it from this repo: CAD, PCB files, firmware, sim assets, training code, bill of materials
- [ ] A write-up shows the data: actuator characterization plots, sim-to-real gap, tracking error, and what broke

## Scope

A robot that walks well on flat ground, with every layer from motor to policy built or understood by me. Everything else is cut or deferred.

**In scope**
- 8 custom quasi-direct-drive actuators (hip flexion + knee per leg): bought BLDC motor, my own transmission (planetary or timing belt, TBD), output encoder, my own housing
- Motor driver: off-the-shelf first (v1), then my own PCB and FOC firmware (v2)
- CAN bus protocol and a real-time joint control loop
- Legs, body, battery and power distribution, onboard computer, IMU, hardware e-stop
- Simulation model with a measured actuator model
- RL walking policy (flat ground, slight unevenness) and sim-to-real transfer
- Open-source release: CAD, PCB, firmware, training code, write-up

**Out of scope:** vision, mapping, navigation, stairs, arms, winding my own motors, custom battery management (use a commercial BMS), rough-terrain and parkour locomotion.

**Stretch (only after G5):** hip ab/adduction joints (12-DOF v2; the body leaves room for them), MPC baseline with a head-to-head comparison against the RL policy, my own IMU or power board, quantified push-recovery tests.

## Design targets

These are starting estimates, not specs. Each one gets validated in simulation before parts are ordered, and the validated value is recorded in a decision record (see [Documentation system](#documentation-system)).

| Parameter | Starting target | Validated value |
|---|---|---|
| Robot mass | ~2.5-3 kg (was 6-8 kg; 12-DOF estimate was 3.8 kg, see [0005](docs/decisions/0005-8-dof-planar-legs.md)) | |
| Leg segments | 0.15 m thigh, 0.15 m shank (was 0.2 m; see [0003](docs/decisions/0003-gear-ratio-and-sizing.md)) | |
| Gear ratio | 9:1 (was 8:1 for a 7 kg robot; see [0003](docs/decisions/0003-gear-ratio-and-sizing.md)) | |
| Actuator peak torque | TBD per motor, ~300 Kv outrunner class (Solo-8 reference: 2.5 Nm at 12 A; RCTimer 5010 estimate: 5.15 Nm) | |
| Actuator continuous torque | TBD | |
| Battery | 6S LiPo or Li-ion, ~24 V | |
| CAN buses | 2 buses, 4 actuators each | |
| Control rates | 10 kHz current / 1 kHz joint / 50 Hz policy | |

---

## Rules

1. **Build order:** one actuator → one leg → the whole robot. Simulation and training run in parallel.
2. **Nothing new runs on the ground before it has run suspended in a harness.**
3. **A sub-project is finished when its done-criterion is measured,** not when it looks right.
4. **Cut hardware ambition before cutting the walking demo** (see [Risks and cut lines](#risks-and-cut-lines)).
5. **If it isn't logged, it didn't happen.** Every work session ends with a log entry.

---

## Sub-projects

| # | Sub-project | Deliverable | Done when | Folder |
|---|---|---|---|---|
| A | Simulation model | URDF/MJCF with joint limits, masses, inertias | Robot stands and walks in sim with PD control | `sim/` |
| B | Actuator mechanical design | One actuator: motor, planetary gearbox, encoder, housing | Runs 1 hour under load with no slip or overheating | `hardware/actuator/` |
| C | Driver and FOC firmware | v1 on an off-the-shelf driver, v2 on my own PCB and firmware | Closed-loop torque control; v2 matches v1 | `firmware/`, `hardware/driver-pcb/` |
| D | CAN bus and control loop | Bus protocol, command/state messages, 1 kHz loop | 4 actuators on one bus at 1 kHz with no dropped frames | `control/` |
| E | Actuator characterization and system ID | Torque constant, friction, inertia, bandwidth, thermal limits as plots | Fitted model reproduces measured step responses | `sysid/` |
| F | Single-leg test rig | 2-actuator leg on a vertical boom | Tracks stance and swing trajectory; sim matches within tolerance | `hardware/leg-rig/` |
| G | Body, power and compute | Frame, battery, power distribution, computer, IMU, e-stop | Powers up, stands on PD, e-stop cuts motor power | `hardware/body/` |
| H | RL training pipeline | Isaac Lab or legged_gym env, rewards, domain randomization | Policy walks in sim with randomized dynamics | `training/` |
| I | Deployment runtime | 50 Hz policy inference, state estimation, safety layer | Runs on the robot in a harness without faults | `deploy/` |
| J | Test rig and safety | Harness or gantry, battery handling procedure | Every first run of new code happens suspended | `safety/` |
| K | Baseline controller (stretch) | MPC or classic gait controller | Comparison plots against the RL policy | `baseline/` |
| L | Documentation and release | Repo, build guide, BOM, write-up, demo video | A stranger could order the parts and reproduce the build | `docs/` |

**Dependencies**

```
A ──► H                      (sim model feeds training)
A ──► B                      (sim sizes the actuator)
B + C + D ──► F              (actuator, driver and bus must work before the leg)
E ──► H                      (measured actuator model makes the policy transfer)
F ──► G                      (single leg gates the full body)
G + I ──► first walking run
```

---

## Timeline

Week 1 starts Monday Oct 26, 2026. Each phase lists what gets built and what gets documented. The documentation column is not optional: a gate review cannot pass without it.

Weeks 9-10 fall over the holidays. If I lose time there, G2 slips, not the go/no-go rule.

### Phase 0 · Setup and learning · Weeks 1-2 (Oct 26 - Nov 8)

**Build**
- [x] Create the repo, folder structure (below) and this README
- [ ] Check GPU access for training (local NVIDIA GPU or cloud option); install Isaac Lab or MuJoCo
- [ ] Run an existing open quadruped (Unitree-style URDF) walking with a pretrained or example policy, to learn the pipeline
- [ ] Read the Mini Cheetah actuator paper and the moteus / Open Dynamic Robot Initiative designs
- [ ] Rough-size the actuator from the simulated peak torque and speed of the ~2.5-3 kg 8-DOF robot
- [ ] Choose a BLDC motor and order: one motor, one off-the-shelf FOC driver, an encoder, a bench supply, a debug probe
- [ ] Get that motor spinning in position and torque control

**Document**
- [ ] `docs/decisions/0001-motor-selection.md`: candidates compared, sizing math, why this one
- [ ] `docs/decisions/0002-sim-stack.md`: Isaac Lab vs MuJoCo/MJX, GPU situation
- [ ] `docs/reading-notes.md`: one paragraph per paper or reference design, what I'm borrowing
- [ ] `docs/bom.md` started, with real prices for what I ordered
- [ ] Sizing script and its output plot in `sim/sizing/`
- [ ] First log entry and first short video (motor spinning)

### Phase 1 · Actuator v1 · Weeks 3-7 (Nov 9 - Dec 13) → **G1**

Sub-projects: **B**, **C** (v1), **E** (start), **A** (in parallel)

**Build**
- [ ] CAD the actuator: motor mount, single-stage planetary, output bearing, encoder mount, housing
- [ ] Print or machine it, assemble, measure backlash
- [ ] Bring up FOC torque control on the off-the-shelf driver
- [ ] Build a test stand: fixed actuator, lever arm or load cell
- [ ] Measure torque constant, friction, reflected inertia, torque bandwidth, thermal rise
- [ ] 1-hour run under load
- [ ] Sim: first URDF/MJCF of the full robot standing under PD control

**Document**
- [ ] CAD exported to `hardware/actuator/` (native file + STEP + STL), with a revision note
- [ ] Driver configuration (gains, limits, calibration) committed as files, not screenshots
- [ ] Test record for each characterization test, with raw data in `data/` and the plotting script
- [ ] Plots: torque vs current (Kt fit), friction vs speed, step response, bandwidth (Bode), temperature vs time
- [ ] Backlash measurement and method
- [ ] `docs/decisions/0003-gear-ratio.md`: validated gear ratio and torque numbers, update the design targets table
- [ ] Gate review G1 + milestone video

### Phase 2 · Single leg · Weeks 8-12 (Dec 14 - Jan 17) → **G2 (go/no-go)**

Sub-projects: **D**, **F**, **E** (model fit), **J**, **A/H** (in parallel)

**Build**
- [ ] Build 1 more actuator (2 total)
- [ ] CAN protocol: command and state message definitions, IDs, rates
- [ ] 1 kHz joint control loop on the host; measure loop jitter and dropped frames
- [ ] Leg CAD and build; vertical boom rig
- [ ] Harness and battery handling procedure (J), before the leg moves under power
- [ ] Track stance and swing trajectories on the boom
- [ ] Fit the actuator model and put it in sim; compare sim leg vs real leg on the same trajectories
- [ ] H: training environment set up; first policy walking in sim on the nominal model

**Document**
- [ ] `control/PROTOCOL.md`: message layout, IDs, units, scaling, timeout and fault behavior
- [ ] Loop timing plot: period histogram and jitter, dropped frame count over a long run
- [ ] `safety/procedures.md`: harness use, e-stop, battery charging and storage, first-power checklist
- [ ] Tracking plots: commanded vs measured joint angles and foot position, RMS error
- [ ] Sim-vs-real overlay plots for the leg, with the tolerance I'm judging against stated up front
- [ ] Actuator model parameters in `sysid/actuator_model.yaml` and the fit script
- [ ] **Gate review G2 with an explicit go/no-go decision** in `docs/decisions/`. If no-go, record which fallback I'm taking
- [ ] Milestone video (leg on the boom)

### Phase 3 · Driver v2 and actuator production · Weeks 13-15 (Jan 18 - Feb 7) → **G3**

Sub-projects: **C** (v2), **B** (×8)

**Build**
- [ ] Driver PCB in KiCad: schematic review, layout, order
- [ ] FOC firmware on STM32: current loop at ≥10 kHz, encoder, CAN, faults
- [ ] Bring up v2 on the bench, then in the leg; compare against v1
- [ ] In parallel: build the remaining 6 actuators, each with a quick acceptance test
- [ ] H: domain randomization, latency and noise modeling; policy walks with randomized dynamics

**Document**
- [ ] KiCad project + fab outputs (Gerbers, BOM, pick-and-place) per revision in `hardware/driver-pcb/revN/`
- [ ] Bring-up log for each board revision: what was wrong, what was patched
- [ ] Current loop timing measured on the scope, not assumed
- [ ] v1 vs v2 comparison table and plots, same tests as G1
- [ ] Actuator acceptance sheet: one row per serial number (Kt, friction, backlash, pass/fail)
- [ ] Training config and reward definitions committed with every policy checkpoint I keep
- [ ] Gate review G3. If v2 is behind, record the cut-line decision (stay on v1 for all 8)

### Phase 4 · Full robot · Weeks 16-19 (Feb 8 - Mar 7) → **G4**

Sub-projects: **G**, **D** (2 buses), **I**, **J**

**Build**
- [ ] Body frame, battery mount, power distribution, BMS, hardware e-stop
- [ ] Onboard computer and IMU; 2 CAN buses, 4 actuators each, at 1 kHz
- [ ] Assemble the robot; update the sim model with measured masses and inertias
- [ ] Deployment runtime: 50 Hz inference, state estimation, safety layer (joint limits, torque limits, watchdog)
- [ ] Stand under PD control in the harness; test the e-stop under load

**Document**
- [ ] Wiring diagram and power distribution schematic
- [ ] Weighed mass breakdown per part, compared with the CAD estimate
- [ ] Updated URDF/MJCF tagged with the robot revision
- [ ] e-stop test: video plus what got cut and how fast
- [ ] Bus load and timing with 8 actuators
- [ ] `deploy/SAFETY.md`: every limit and watchdog in the runtime, and what triggers it
- [ ] Gate review G4 + milestone video (standing in harness)

### Phase 5 · Walking · Weeks 20-24 (Mar 8 - Apr 11) → **G5**

Sub-projects: **H**, **I**, **E** (refine)

**Build**
- [ ] Run the policy in the harness, robot suspended, then feet barely touching
- [ ] Ground runs with the harness slack, then untethered
- [ ] Iterate: compare real vs sim, improve the actuator model, widen randomization, retrain
- [ ] Velocity command tracking: forward/backward, turning
- [ ] Push recovery test
- [ ] 20-minute battery run

**Document**
- [ ] One run record per hardware run: policy checkpoint, firmware version, config, outcome, log file
- [ ] Sim-to-real gap plots: same command, sim vs real joint trajectories and base velocity
- [ ] Velocity tracking error plots (commanded vs estimated)
- [ ] Push recovery video
- [ ] Battery run log: voltage and current vs time, total runtime
- [ ] Failure log: every fall, every broken part, and the cause
- [ ] Gate review G5 + walking video

### Phase 6 · Release · Weeks 25-26 (Apr 12 - Apr 25) → **G6**

Sub-project: **L**

**Build / Document**
- [ ] Build guide: order parts → print/machine → assemble actuator → leg → body → flash → calibrate → first run
- [ ] Final BOM with part numbers, suppliers and real costs
- [ ] License chosen and applied (hardware and software)
- [ ] Write-up: actuator characterization, sim-to-real gap, tracking error, what broke and what I'd change
- [ ] Demo video
- [ ] Ask one person to read the build guide cold and note where they get stuck; fix those spots
- [ ] Gate review G6, tag `v1.0`

---

## Documentation system

The idea is that the write-up at the end is mostly assembled from things already in the repo. Every artifact below is a markdown file or data file committed to git.

### What gets written, and when

| When | What | Where |
|---|---|---|
| Every work session | Log entry | `docs/log/YYYY-MM-DD.md` |
| Every test that produces numbers | Test record + raw data + plot script | `docs/tests/`, `data/`, `<subproject>/scripts/` |
| Every hardware run of new code | Run record (inside the test record) | `docs/tests/` |
| Every non-obvious choice (part, ratio, architecture, cut line) | Decision record | `docs/decisions/NNNN-title.md` |
| Every gate | Gate review | `docs/gates/G<n>.md` |
| Every hardware/firmware revision | Changelog entry + git tag | `CHANGELOG.md` |
| Every milestone, including failures | Video | linked from the log entry and gate review |
| Every 2 weeks | Public update (GitHub, X, YouTube or blog) | linked in `docs/updates.md` |
| Every purchase | BOM row with real price | `docs/bom.md` |

### Templates

**Log entry** · `docs/log/YYYY-MM-DD.md`

```markdown
# YYYY-MM-DD · <sub-project letter> · <one-line summary>
Time spent: Xh

## Goal for this session
## What I did
## What broke / surprised me
## Data
<plot or photo, link to test record if any>
## Next
```

**Test record** · `docs/tests/YYYY-MM-DD-<name>.md`

```markdown
# <Test name>
Date · Sub-project · Hardware rev · Firmware commit · Config file

## Question this test answers
## Setup
<photo, fixture, instruments, sample rate>
## Procedure
## Pass criterion (written before running)
## Results
<plots, table>
Raw data: data/<file>.csv  ·  Script: <path>
## Conclusion: PASS / FAIL / INCONCLUSIVE
```

**Decision record** · `docs/decisions/NNNN-<title>.md`

```markdown
# NNNN · <Decision>
Date · Status: proposed / accepted / superseded by NNNN

## Context
## Options considered
## Decision
## Why
## What would make me revisit this
```

**Gate review** · `docs/gates/G<n>.md`

```markdown
# G<n> · <gate criterion>
Target date · Actual date

## Criterion
## Evidence
<links to test records, plots, video>
## Met? YES / NO
## If no: what I'm cutting or changing (link decision record)
## Schedule impact
## What I learned in this phase
```

### Logging and data conventions

- Every test logs to CSV or MCAP, with a header row and units in column names (`torque_Nm`, `current_A`, `t_s`).
- Raw data is never edited. Processing happens in a script that reads raw data and writes plots.
- Every plot in the repo can be regenerated by running one script.
- Every run record names the firmware commit, config file and policy checkpoint, so any result can be reproduced.
- Large files (video, big logs, checkpoints) go in Git LFS or a linked external store, not plain git.

---

## Repo structure

```
Robot-Dog/
├── README.md               this file: plan, timeline, status
├── CLAUDE.md               guidance for AI agents helping on this project
├── CHANGELOG.md
├── docs/
│   ├── templates/          copy-ready templates for the records below
│   ├── log/                dated session logs
│   ├── tests/              test records
│   ├── decisions/          decision records
│   ├── gates/              gate reviews
│   ├── bom.md
│   ├── reading-notes.md
│   ├── updates.md          links to public updates
│   └── build-guide/        (Phase 6)
├── data/                   raw test data
├── sim/                    A: URDF/MJCF, sizing scripts
├── hardware/
│   ├── actuator/           B: CAD, STEP, STL
│   ├── driver-pcb/         C: KiCad, per revision
│   ├── leg-rig/            F
│   └── body/               G
├── firmware/               C: FOC firmware
├── control/                D: CAN protocol, joint loop
├── sysid/                  E: characterization, model fits
├── training/               H: environments, rewards, configs
├── deploy/                 I: onboard runtime, safety layer
├── safety/                 J: procedures, harness
└── baseline/               K: stretch
```

---

## Tools and stack

- **CAD:** Onshape or Fusion 360 · **PCB:** KiCad
- **Firmware:** C/C++ on STM32 (Rust optional). References: moteus, ODrive, SimpleFOC
- **Sim and training:** Isaac Lab or MuJoCo with legged_gym and rsl_rl, PPO. Isaac Lab needs an NVIDIA RTX-class GPU; otherwise rent a cloud GPU or use MuJoCo MJX
- **Workflow:** Git from day one, CSV or MCAP logs, plots in Python

**Equipment:** bench supply with current limit, oscilloscope or logic analyzer, 3D printer access, soldering station, hardware e-stop, fire-safe LiPo bag.

## Budget

Approximate planning figures, not quotes. Real prices go in `docs/bom.md` as parts are bought.

| Item | Estimate (USD) | Actual |
|---|---|---|
| 8 BLDC motors (+1-2 spares) + transmission parts + encoders | Motors under $1,000 CAD total; rest TBD after motor choice (0001) | |
| Off-the-shelf drivers for v1 (2-3) | 150-300 | |
| Custom driver PCBs (fab and parts, a few revisions) | 200-500 | |
| Frame, printing material, fasteners | 150-300 | |
| Battery, charger, power distribution, e-stop | 150-300 | |
| Onboard computer (Pi 5 or Jetson-class) + IMU | 150-400 | |
| Test rig: boom, harness, load cell, bench supply | 100-300 | |
| **Total** | **~2,000-3,500** | |

## Risks and cut lines

The biggest risk is scope: 8 custom actuators plus a custom PCB plus RL is a lot for one person.

| Risk | Cut line or fallback |
|---|---|
| Custom driver PCB takes too long or fails | Stay on an off-the-shelf driver (moteus or ODrive class) for all 8 joints. Keep the custom PCB as a side project on one actuator |
| Actuators too weak, hot or backlashy | Re-size in sim, change the gear ratio, or buy commercial actuators for the knees only |
| Policy works in sim, falls on the robot | Improve the actuator model from system ID, widen domain randomization, add latency and noise modeling. Fall back to a PD-based stand and trot gait |
| Training compute | Rent a cloud GPU or switch to MuJoCo MJX |
| Behind schedule at the end of the single-leg phase | Ship a single leg hopping on a boom (the robot is already the reduced 2-DOF-per-leg version, see [0005](docs/decisions/0005-8-dof-planar-legs.md)) |
| Hardware damage or injury (LiPo, pinch points, falls) | Harness for every first run, e-stop in hand, current limits in firmware, LiPo in a bag while charging |
| Losing momentum | Public update every two weeks with a short video or plot |

**Week 12 checkpoint:** if the single leg is not tracking trajectories, drop to a fallback rather than building 12 actuators on an unproven design. Record the decision either way.

---

## License

To be decided before the first public release (see Phase 6).
