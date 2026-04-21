## Overview
This folder contains the proposal materials and Simulink models for a quarter-car active suspension project. The project studies how a vehicle suspension responds to road disturbances and compares passive and active control strategies.

The underlying quarter-car model includes:
- **Sprung mass** `m_s = 250 kg` (vehicle body / cabin)
- **Unsprung mass** `m_u = 40 kg` (wheel / axle assembly)
- **Suspension spring** `k_s = 15000 N/m`
- **Tire stiffness** `k_t = 150000 N/m`
- **Road input** `r(t)` (bump / step / sinusoidal / noise input)
- **Actuator force** `u(t)` for the active case

The intended equations are:

\[
m_s \ddot{x}_s = -k_s(x_s-x_u) - c_s(\dot{x}_s-\dot{x}_u) + u
\]

\[
m_u \ddot{x}_u = k_s(x_s-x_u) + c_s(\dot{x}_s-\dot{x}_u) - k_t(x_u-r) - u
\]

where `c_s` is the passive damping coefficient.

---

## Files

### 1. `activemodel01.slx`
Primary **active suspension** Simulink model.

#### Current setup identified from the model
- `1/ms = 1/250`
- `1/mu = 1/40`
- `Spring Coefficient = 15000`
- `Tire Coefficient = 150000`
- `Damping Coefficient = 0`
- `Kp = -5000`
- `Kd = -500`
- Road inputs present in the model:
  - `Step` with `After = 0.05`
  - `Step1` with `Time = 1.5`, `After = -0.05`
  - `Sine Wave` with amplitude `0.02`, frequency `10`
  - `Band-Limited White Noise`

#### Purpose
Use this model as the base for the **hybrid active + passive** design by assigning a nonzero damping coefficient and keeping the active controller enabled.

#### Recommended next change
Set:
- `Damping Coefficient = 1000 Ns/m`

This makes the model more realistic by combining:
- passive damping
- active control

---

### 2. `passivemodel.slx`
Baseline **passive suspension** Simulink model.

#### Current setup identified from the model
- `1/ms = 1/250`
- `1/mu = 1/40`
- `Spring Coefficient = 15000`
- `Tire Coefficient = 150000`
- `Damping Coefficient = 0`
- `Kp = -5000`
- `Kd = -500`
- Road inputs present in the model:
  - `Step`
  - `Sine Wave` with amplitude `0.02`, frequency `5`
  - `Band-Limited White Noise`

#### Important note
Although this is labeled as the passive model, the parsed block list shows the damping coefficient is also currently set to **0**, so the model should be checked in Simulink and updated before using it as the passive baseline.

#### Recommended passive baseline
For a proper passive comparison, set:
- controller force `u = 0`
- `Damping Coefficient = 1000 Ns/m`

Use the **same road input** as the active model when comparing results.

---

### 3. `activesuspension.slx`
Earlier active suspension version.

#### Likely role
This appears to be an earlier draft of the active suspension model. Keep it as a backup/reference version unless you specifically need to compare revisions.

---

### 4. `Final Project Proposal.docx`
Written proposal for the active suspension project.

#### Purpose
Contains the project overview, significance, modeling approach, and simulation plan.

---

### 5. `Final Project Proposal.key`
Presentation slides for the project proposal.

#### Purpose
Use this for presenting the project motivation, model structure, and planned workflow.

---

### 6. `active_suspension_diagram.png`
Labeled diagram of the quarter-car active suspension setup.

#### Purpose
Use in the report or presentation to explain:
- sprung mass
- unsprung mass
- spring and damper
- tire stiffness
- road input
- actuator
- controller and sensors

---

## Recommended Model Roles
To avoid confusion, use the files in the following way:

- **Passive baseline:** `passivemodel.slx`
- **Final tuned hybrid model:** `activemodel01.slx`
- **Archive / earlier reference:** `activesuspension.slx`

---

## Recommended Comparison Setup
For the final report/presentation, compare three cases:

### Case A — Passive suspension
- `u = 0`
- `c_s = 1000 Ns/m`

### Case B — Active-only suspension
- controller on
- `c_s = 0`

### Case C — Hybrid active + passive suspension
- controller on
- `c_s = 1000 Ns/m`

Case C should be your **recommended final design** because it balances comfort and actuator effort more realistically.

---

## Tuning Standard
To balance **comfort** and **power consumption**, use the following standard:

### Comfort metric
Use **RMS body acceleration**

\[
a_{RMS} = \sqrt{\frac{1}{T}\int_0^T \ddot{x}_s^2(t)\,dt}
\]

### Power metric
Use **average actuator power**

\[
P_{avg} = \frac{1}{T}\int_0^T |u(t)(\dot{x}_s-\dot{x}_u)|\,dt
\]

### Selection rule
Choose the damping coefficient that:
1. keeps RMS body acceleration within **5% of the best comfort case**, and
2. among those options, uses the **lowest actuator power**.

### Recommended damping value
For the current model and controller settings, a good engineering choice is:

- **`c_s = 1000 Ns/m`**

This gives a good compromise between:
- ride comfort
- control effort
- realism

---

## Outputs to Plot
For each simulation, plot:
- **Body displacement** `x_s(t)`
- **Body acceleration** `xddot_s(t)`
- **Suspension deflection** `x_s - x_u`
- **Actuator force** `u(t)`

Optional:
- **Tire deflection** `x_u - r`

---

## Recommended Next Steps
1. Open `passivemodel.slx` and `activemodel01.slx` in Simulink.
2. Make sure both models use the **same road input**.
3. Set the passive damping coefficient to `1000 Ns/m`.
4. Keep the controller enabled only in the active/hybrid case.
5. Run passive, active-only, and hybrid comparisons.
6. Record the outputs listed above.
7. Use the hybrid result as the final recommended design.

---

## Software
These files are intended for **MATLAB / Simulink**.

Recommended toolbox/environment:
- MATLAB
- Simulink

---

## Notes
- The current models include several road input blocks, but not all may be connected at once.
- The passive model should be verified carefully, since its parsed parameters currently show zero damping as well.
- For a fair comparison, always use the same disturbance signal and simulation time across all cases.
