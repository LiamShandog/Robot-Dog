# 0005 · 8 DOF with planar legs, ODRI Solo-8 as the reference design
2026-10-29 · Status: accepted

## Context

- I set a budget of $40 CAD per motor, and the robot was sized down to fit it. Reverse sizing around the RCTimer 5010 260KV (5.15 Nm knee at 9:1, 0.15 m legs, 3× trot load) allowed a maximum robot mass of about 3.27 kg.
- The 12-DOF mass budget came to about 3.8 kg: 12 actuators at ~225 g, battery ~350 g, computer ~100 g, drivers and wiring ~250 g, frame ~400 g. That is over the limit.
- The knee can't be removed. It does most of the stance work, and without it the foot can't lift during swing. The joint that can go is hip ab/adduction (the sideways one).
- ODRI's Solo-8 is a working open-source 8-DOF quadruped: two joints per leg (hip flexion, knee), all in the sagittal plane. It weighs about 2 kg. Each actuator is a T-Motor Antigravity 4004 300KV motor with a 9:1 two-stage timing belt, rated 2.5 Nm at 12 A, at about 150 g per module.

## Options considered

| Option | Effect | Cost |
|---|---|---|
| 12 DOF, 3× load factor | Full 3D legs | ~3.8 kg, over the 3.27 kg limit |
| 12 DOF, 2× load factor | Fits on paper (~4.9 kg allowed) | Risk of undersizing; 13-14 motors to buy |
| **8 DOF (drop hip ab/ad)** | ~2.9 kg estimate, under the limit; 9-10 motors to buy; planar kinematics | No sideways walking, limited turning, weak sideways push recovery |

## Decision

- **8 DOF**: hip flexion and knee on each leg. No hip ab/adduction.
- **Reference design: ODRI Solo-8** ([hawkridge refurbished build](https://github.com/ToolKnox/hawkridge-odri-solo8-open-quadruped-robot)). I'm using it for inspiration, sizing references and part choices. I'm not using its CAD, PCB or firmware files: every part of this robot is designed by me.
- Success criteria change: sideways velocity tracking is dropped; forward/backward tracking plus limited turning remain.
- Body designed so a hip ab/ad joint can be added later (12-DOF v2, stretch goal).
- Ratio, leg lengths and mass target: re-sized in [0003 revision 2](0003-gear-ratio-and-sizing.md). Still open: motor (0001) and transmission type (planetary vs belt).
- Motor budget: no hard per-motor cap, but all motors together stay under $1,000 CAD.

## Why

This requires fewer actuators, is proven to work, and fits my timeline better.

## What would make me revisit this

- Sim shows planar legs can't turn well enough to be useful.
- Push recovery without a sideways step is too weak to meet the success criterion.
- Mass comes in well under budget, leaving room for ab/ad actuators.
