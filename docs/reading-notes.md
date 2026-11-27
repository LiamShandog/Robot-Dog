# Reading notes

One section per paper or reference design: what it is, the key numbers, and what I'm borrowing.

## Mini Cheetah actuator paper
## moteus
## Open Dynamic Robot Initiative

## humanoid.guide: "Hip, Knee, Ankle: How Humanoid Robots Budget Their Leg Joints and Actuators"
<https://humanoid.guide/humanoid-leg-joint-architecture-hip-knee-ankle/> · read 2026-10-30

**What it is:** An explainer about humanoid robots that promotes "The Humanoid Actuation Report". Its hip/knee sizing guidance comes from CubeMars, which sells actuators.

**Key claims and numbers:**
- "The hip dominates." Its reasons: the hip is a 3-DOF ball joint (yaw, roll, pitch) that "redirects the full body weight through multiple axes", sweeps about ±90° in flexion, and has to survive squatting, jumping and landing.
- CubeMars sizes hip motors for peak torque 3–5× the rated value, and knee motors for 2–4×. The knee's priorities are compactness and thermal stability, because it works in every step.
- The FAQ partly contradicts the headline: "The knee often gets the single highest published number because it works as a pure hinge under the whole body's weight."
- Unitree G1 knee: 90 N·m peak (120 N·m on the EDU). No G1 hip figure is given. The H1's most loaded hip and knee axes use the M107 motor, which peaks at 360 N·m; the article doesn't split that between hip and knee.
- No moment-arm, posture or single-leg-support reasoning. The case is qualitative.

**Does it apply to this robot:** Mostly no. The high hip demand comes from humanoid-specific loads:
- Hip roll in single-leg support, where the center of mass sits far to the side of the stance foot. This robot doesn't have that joint (see [0005](decisions/0005-8-dof-planar-legs.md)).
- Hip pitch holding up an upright torso above it. On this robot four legs share the torso's weight.

In a planar quadruped leg, every joint in the stance leg carries the same ground reaction force. What sets the torque at each joint is the horizontal moment arm from that joint to the foot. With the foot under the hip, the knee carries the larger torque.

**What I'm borrowing:** The idea of separate peak/rated overload factors per joint, which belongs in 0001 (motor selection). Also the reminder to size for landing impacts, not only for standing.

## BXI Robotics: "Robot Dog and Exoskeleton Actuator Guide"
<https://www.bxirobotics.com/blog/quadruped-exoskeleton-actuator-guide> · published 2026-07-27, no author listed · read 2026-10-30

**What it is:** A sizing blog from BXI Robotics, which sells actuators. It promotes BXI's joint motors (BXI5014-19, 5018-19, 7010-19, 8515-19), cites no sources or test data, and calls its torque ranges "engineering orders of magnitude".

**Key claims and numbers:**
- Quadrupeds and exoskeletons both benefit from high torque density, backdrivability and compliant force control, which is why low-ratio quasi-direct-drive (QDD) actuators are common.
- High-ratio harmonic drives have more reflected inertia and friction, so they are "often less suitable for highly dynamic legs".
- Backdrivability lowers peak drivetrain loads from "repeated ground impact" and allows torque to be estimated from current.
- On hip vs knee: "the knee usually the most demanding joint due to its longer moment arm". In BXI's own mid-size recommendation, hips and knees use the **same** motor (BXI7010-19). A lighter motor (BXI5018-19) is only suggested for hip abduction.
- "Keeping leg mass low matters twice here — leg inertia sets both stride frequency and battery life."
- A typical quadruped has 12 joints. Robot dogs from 10 to 50 kg typically need 20–60 N·m peak per joint.
- Product numbers:

  | Motor | Peak torque | Mass | Use |
  |---|---|---|---|
  | BXI7010-19 | 50 N·m | 0.8 kg | 10–30 kg quadruped hips and knees |
  | BXI5018-19 | 35 N·m | 0.55 kg | Lighter axes such as hip abduction |
  | BXI5014-19 | 25 N·m | 0.5 kg | Exoskeleton |
  | BXI8515-19 | 150 N·m (40 N·m rated) | — | Quadrupeds over 40 kg |

  All of these use a 19.5:1 planetary reduction.
- The page contradicts itself on large quadrupeds: the text says their needs are "well beyond" 20–60 N·m, while the table gives 60–150 N·m.
- The exoskeleton sections (wearer backdrive effort, every gram carried by the wearer, rehab safety) don't apply here.

**Does it apply to this robot:** Partly. This robot is far below the page's 10 kg minimum: the target is ~2.5–3 kg, and knee torque is ~5 N·m at 9:1 ([0003](decisions/0003-gear-ratio-and-sizing.md)). Its torque ranges don't scale down to this robot. The architecture points still apply: low reduction, backdrivability, light legs.

**What I'm borrowing:**
- A second source, independent of my own calculation, that the knee is the most loaded joint because of its moment arm.
- One motor for both hip and knee. Even a vendor with several motor sizes uses the same one for both.
- Leg inertia is a design target. This matters for where the knee motor goes: at the knee, or at the hip with a belt.
