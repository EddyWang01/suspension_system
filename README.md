# Quarter-Car Suspension Simulations

This folder contains three MATLAB/Simulink models for a quarter-car suspension study:

- `passivemodel.slx`
- `activemodel_final.slx`
- `combinedmode_final.slx`

The models compare a passive suspension, an active suspension, and a combined active plus passive suspension using the same basic quarter-car plant.

## Model

The quarter-car model uses:

- Sprung mass: `m_s = 250 kg`
- Unsprung mass: `m_u = 40 kg`
- Suspension spring stiffness: `k_s = 15000 N/m`
- Tire stiffness: `k_t = 150000 N/m`
- Road displacement input: `r(t)`
- Actuator force for active models: `u(t)`

The governing equations are:

```text
m_s * x_s_ddot = -k_s * (x_s - x_u) - c_s * (x_s_dot - x_u_dot) + u

m_u * x_u_ddot =  k_s * (x_s - x_u) + c_s * (x_s_dot - x_u_dot)
                  - k_t * (x_u - r) - u
```

where:

- `x_s` is body displacement
- `x_u` is wheel/unsprung displacement
- `c_s` is the suspension damping coefficient
- `u = 0` for the passive model

All three models use `VariableStepAuto` from `t = 0` to `t = 10 s`.

## Simulation Files

### `passivemodel.slx`

Baseline passive suspension model.

Current parsed setup:

- `1/ms = 1/250`
- `1/mu = 1/40`
- `Spring Coefficient = 15000`
- `Tire Coefficient = 150000`
- `Damping Coefficient = 500`
- No `Kp` or `Kd` actuator controller blocks are connected to the plant
- Body force sum has spring and damper inputs only
- Wheel force sum has damper, spring, and tire inputs

Road/disturbance setup:

- Connected road input: `Band-Limited White Noise`
- Noise covariance: `[1e-4]`
- Noise sample time: `0.1`
- Noise seed: `[23341]`
- `Step`, `Step1`, and `Sine Wave` blocks are present, but the parsed connections show the tire deflection input is driven by the white-noise block.

Outputs visible in scopes:

- `xs` and `xu`
- `xs dot dot` and `xu dot dot`
- An RMS-style body acceleration display is built from `xs dot dot`.

### `activemodel_final.slx`

Active suspension model using proportional-derivative body feedback.

Current parsed setup:

- `1/ms = 1/250`
- `1/mu = 1/40`
- `Spring Coefficient = 15000`
- `Tire Coefficient = 150000`
- `Damping Coefficient = 0`
- `Kp = -2000`
- `Kd = -1000`
- Controller output `u` is added to the body force sum and subtracted from the wheel force sum

Road/disturbance setup:

- Connected road input: sum of `Step` and `Step1`
- `Step`: final value `0.05`
- `Step1`: time `1.5 s`, final value `-0.05`
- `Sine Wave` and `Band-Limited White Noise` blocks are present, but the parsed connections show the road input is driven by the two step blocks.

Outputs visible in scopes:

- `xs` and `xu`
- `xs dot dot` and `xu dot dot`
- An RMS-style body acceleration display is built from `xs dot dot`.

### `combinedmode_final.slx`

Combined active plus passive suspension model.

Current parsed setup:

- `1/ms = 1/250`
- `1/mu = 1/40`
- `Spring Coefficient = 15000`
- `Tire Coefficient = 150000`
- `Damping Coefficient = 1000`
- `Kp = -2000`
- `Kd = -1000`
- Controller output `u` is added to the body force sum and subtracted from the wheel force sum

Road/disturbance setup:

- Connected road input: `Band-Limited White Noise`
- Noise covariance: `[1e-4]`
- Noise sample time: `1`
- Noise seed: `[23341]`
- `Step`, `Step1`, and `Sine Wave` blocks are present, but the parsed connections show the tire deflection input is driven by the white-noise block.
- `Step1` is configured with time `1.3 s` and final value `-0.05`, but it is not the connected road input in the current model.

Outputs visible in scopes:

- `xs` and `xu`
- `xs dot dot` and `xu dot dot`
- An RMS-style body acceleration display is built from `xs dot dot`.

## Comparison Summary

| File | Role | Damping `c_s` | Controller | Connected Road Input |
| --- | --- | ---: | --- | --- |
| `passivemodel.slx` | Passive baseline | `500` | None | White noise, `Ts = 0.1` |
| `activemodel_final.slx` | Active-only model | `0` | `Kp = -2000`, `Kd = -1000` | `0.05 m` step plus `-0.05 m` step at `1.5 s` |
| `combinedmode_final.slx` | Active plus passive model | `1000` | `Kp = -2000`, `Kd = -1000` | White noise, `Ts = 1` |

Because the current files do not all use the same connected road input, compare the saved scope behavior carefully. For a direct performance comparison, use the same connected disturbance source in all three models before collecting final results.

## Suggested Metrics

Use the following outputs when comparing the models:

- Body displacement: `x_s(t)`
- Wheel displacement: `x_u(t)`
- Body acceleration: `x_s_ddot(t)`
- Wheel acceleration: `x_u_ddot(t)`
- Suspension deflection: `x_s - x_u`
- Tire deflection: `x_u - r`
- Actuator force: `u(t)` for the active and combined models

Recommended comfort metric:

```text
a_RMS = sqrt((1/T) * integral_0^T x_s_ddot(t)^2 dt)
```

Recommended control-effort metric for active models:

```text
P_avg = (1/T) * integral_0^T |u(t) * (x_s_dot - x_u_dot)| dt
```

## Recommended Workflow

1. Open the three `.slx` files in MATLAB/Simulink.
2. Verify which road source is connected in each model.
3. For a fair comparison, connect the same road input in all three models.
4. Run each simulation over `0` to `10 s`.
5. Compare displacement, acceleration, suspension travel, and actuator effort.
6. Use `combinedmode_final.slx` as the final active plus passive design case.

## Software

These files are intended for MATLAB and Simulink.
