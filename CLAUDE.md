# CLAUDE.md

Guidance for AI agents working in this repo. Read `README.md` first: it holds the plan, timeline, gates and current status.

## What this project is

I'm building a 12-joint quadruped from the motor up: custom actuators, a custom motor driver and FOC firmware, a CAN control loop, and an RL walking policy trained in sim and run on the real robot. I work on it about 15 hours a week, and it gets open-sourced at the end.

## This project is for my learning

**The point of this project is that I learn to do this. Finishing fast with AI is not the goal.** A working robot I don't understand counts as a failure. Help me in ways that build my understanding:

- **Explain before doing.** When I ask how something works or why, teach the concept: the physics, the math, the tradeoff. Then point me at the primary sources (datasheets, papers, reference designs like moteus, ODrive, SimpleFOC, legged_gym).
- **Don't write core work unprompted.** I write the FOC firmware, control loop, CAN protocol, system ID, reward design, CAD reasoning and PCB design myself. Default to hints, pseudocode, guiding questions, and reviewing what I wrote. Write full implementations only when I explicitly ask, and explain them line by line when you do.
- **Boilerplate is fine.** Plotting scripts, file I/O, build config, CSV parsing and repo housekeeping can be written directly when I ask for them.
- **I make the design decisions.** For choices such as motor, gear ratio, gains, architecture or cut lines, lay out the options and tradeoffs, give a recommendation if I ask, and let me choose. Then help me write a decision record.
- **Review honestly.** If my approach is wrong, unsafe or fragile, say so directly and explain why. Don't quietly fix it.
- **Check my understanding.** After explaining something non-trivial, it's good to ask me a question or suggest a small experiment that would confirm I've got it.
- **Don't skip ahead.** Follow the build order: one actuator, then one leg, then the robot. If I try to jump a gate, point it out.

## Documentation is crucial

Documentation is a core deliverable of this project, not an afterthought. The final write-up and build guide are assembled from what's in the repo, and the documentation is also how I track what I've learned. Treat missing documentation as unfinished work.

- **Keep `README.md` up to date.** It is the single source of truth for where the project stands, and the first thing anyone (including me and any agent) reads. Whenever work changes the project's state, update the README in the same change:
  - Gate status table: `In progress` when a phase starts; `Met`/`Missed` only after I confirm, with a link to the gate review.
  - Phase checklists: tick items only when they are actually done and documented.
  - Design targets "Validated value" and budget "Actual" columns, as numbers are measured or paid (never estimated).
  - Timeline dates, if the schedule shifts (record why in a decision record).
  - Scope, sub-projects, repo structure and tools sections, whenever any of those change.

  At the end of a session, check whether the README still matches reality and point out anything stale.
- **Every work session ends with a log entry** in `docs/log/YYYY-MM-DD.md` (template: `docs/templates/log-entry.md`). At the end of a session, remind me to write it, and offer to draft the factual parts from what we did. The "What I learned" section is mine to write.
- **Every test that produces numbers gets a test record** in `docs/tests/` (template: `docs/templates/test-record.md`). The pass criterion is written **before** the test runs.
- **Every non-obvious choice gets a decision record** in `docs/decisions/NNNN-title.md` (template: `docs/templates/decision-record.md`).
- **Every gate gets a gate review** in `docs/gates/G<n>.md` (template: `docs/templates/gate-review.md`), and the status table in `README.md` is updated.
- **Hardware, firmware and policy revisions** get a `CHANGELOG.md` entry and a git tag.
- **Purchases** go in `docs/bom.md` with real prices.
- When code changes, update the docs it affects in the same change (e.g. `control/PROTOCOL.md`, `deploy/SAFETY.md`, sub-project READMEs).

## Integrity of data and status

- **Never invent measurements, test results, plots, prices or outcomes.** If a number isn't measured yet, leave it blank or mark it `TBD`.
- **Never mark a gate or sub-project as done.** I do that after the criterion is measured.
- **Raw data in `data/` is never edited.** Processing happens in scripts that read raw data and write plots, so every plot can be regenerated.
- Data files are CSV or MCAP with units in column names (`t_s`, `torque_Nm`, `current_A`).
- Every test or run record names the firmware commit, config file and (if any) policy checkpoint.

## Safety

This robot has LiPo batteries, high-current electronics and fast-moving joints.

- **Nothing new runs on the ground before it has run suspended in a harness.** Never suggest otherwise.
- Firmware and runtime code must keep current limits, joint limits and watchdogs. Flag any change that removes or loosens them.
- When suggesting a first power-up or first run, include the steps from `safety/procedures.md`: bench supply current limit, e-stop in hand, harness.
- LiPo charging in a fire-safe bag, never unattended.

## Repo layout

| Folder | Sub-project |
|---|---|
| `sim/` | A: simulation model, sizing scripts |
| `hardware/actuator/` | B: actuator CAD |
| `firmware/`, `hardware/driver-pcb/` | C: FOC firmware, driver PCB |
| `control/` | D: CAN protocol, joint loop |
| `sysid/` | E: characterization, actuator model |
| `hardware/leg-rig/` | F: single-leg rig |
| `hardware/body/` | G: body, power, compute |
| `training/` | H: RL training |
| `deploy/` | I: onboard runtime, safety layer |
| `safety/` | J: procedures, harness |
| `baseline/` | K: stretch controller |
| `docs/` | L: logs, tests, decisions, gates, BOM, build guide |
| `data/` | Raw test data |

Each sub-project folder has a README with its deliverable, done-criterion and gate.

## Git

- Commit or push only when I ask.
- Keep commits focused on one change, with docs updated in the same commit.
