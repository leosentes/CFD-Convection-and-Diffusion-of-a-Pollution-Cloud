# CFD: Convection and Diffusion of a Pollution Cloud

**Author:** Léo Sentes

This project studies the **convection-diffusion of a pollutant cloud** through two complementary approaches: a simulation in **STAR-CCM+** and a custom **Python** implementation. The goal was to model the physical phenomenon, understand the influence of key parameters, and validate a from-scratch numerical solver against a professional CFD software.

## 🎯 Objectives

**Part 1 — STAR-CCM+**
- Model a pollution cloud subject to convection and diffusion.
- Study the influence of mesh refinement.
- Study the influence of the initial velocity `u0`.
- Study the influence of the diffusion coefficient `gamma`.

**Part 2 — Python**
- Implement an algorithm solving the same convection-diffusion problem.
- Validate the Python code against the STAR-CCM+ results.

## 🧪 Physical Model

The pollutant transport is governed by the convection-diffusion equation for a passive scalar Φ:

```
∂Φ/∂t + u·∇Φ = γ∇²Φ
```

where `u` is the velocity field (convection) and `γ` is the diffusion coefficient.

**Air properties used:**
- Density: 1.10415 kg/m³
- Dynamic viscosity: 1.855505×10⁻⁵ Pa·s

## 🧵 STAR-CCM+ Setup

- **Mesh:** base size 1 m, target size 1% of base size, growth rate 1.000001 (chosen to keep a constant time step).
- **Velocity field:** initial condition `u0` along x, `v0 = 0`.

Eight simulations were run to isolate the effect of each parameter:

| Simulation | u0 [m/s] | gamma [m²/s] | Mesh (cells) |
|:---:|:---:|:---:|:---:|
| 1 | 1   | 0.001  | 7 056  |
| 2 | 1   | 0.002  | 7 056  |
| 3 | 1   | 0.0005 | 7 056  |
| 4 | 0.5 | 0.001  | 7 056  |
| 5 | 2   | 0.001  | 7 056  |
| 6 | 1   | 0.001  | 14 400 |
| 7 | 1   | 0.001  | 28 224 |
| 8 | 1   | 0.001  | 49 284 |

**Observations:**
- The cloud spreads out due to the diffusive term (information transmitted in all directions) and shifts to the right due to the convective term (information transmitted along the velocity direction).
- Increasing `u0` shifts the concentration peak further along x and increases the spreading caused by convection.
- Decreasing `gamma` reduces diffusion and increases the peak concentration, without affecting its position (gamma only acts on the diffusive term).
- Refining the mesh increases the peak concentration by reducing numerical diffusion.
- Residuals converge toward ~10⁻⁴ for the passive scalar, confirming convergence of the simulations.

## 🐍 Python Implementation

The Φ field is solved on the interior cells starting from the discretized form of the convection-diffusion equation, with boundary conditions implemented directly in the solver. The velocity field and the resulting concentration distribution reproduce the same qualitative behavior observed in STAR-CCM+ (diffusion in all directions, convection along x).

## ✅ Validation

The Python solver was validated against STAR-CCM+ using the operating conditions of simulation 5, then across all values of `u0` and `gamma`:

- The Python curve sits slightly above the STAR-CCM+ curve, which is expected near strong velocity gradients; the two remain closely aligned with a small overall gap.
- Discrepancies between the Python code and STAR-CCM+ decrease as `u0` decreases (strong velocity gradients being the main source of the gap).
- Discrepancies increase slightly as `gamma` decreases, but remain small overall.

Overall, the Python implementation is validated: it reproduces the STAR-CCM+ results with good accuracy across all tested velocities and diffusion coefficients.

## 📚 Key Takeaways

- The convective term drives the displacement of the cloud along the velocity direction, while the diffusive term drives its spreading.
- Mesh refinement reduces numerical diffusion, bringing the simulation closer to the physical diffusion coefficient.
- A simple, well-implemented finite-difference/finite-volume Python solver can reproduce professional CFD software results with good accuracy.
