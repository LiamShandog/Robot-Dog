# A · Simulation model

**Deliverable:** URDF/MJCF of the robot with joint limits, masses, inertias

**Done when:** Robot stands and walks in sim with PD control

**Gate:** Feeds H (training) and the sizing of B. Sizing scripts go in `sizing/`.

Status: In progress — scene scaffold (`scene.xml`) created; robot body tree (torso + legs) not yet written

## Running the sim

From `Robot-Dog/`, with `.venv` active (it auto-activates on `cd`):

```bash
python -m mujoco.viewer --mjcf sim/scene.xml
```

Viewer controls: `Space` pause/resume, `Backspace` reset, `→` single step while paused, double-click to select a body, `Ctrl` + right-drag to push it. The Joint and Control panels move joints and actuators by hand; the Rendering panel shows contacts, joint axes and centres of mass. Drag an edited XML onto the window to reload it.
