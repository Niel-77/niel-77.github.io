---
layout: post
title: "Theory of Elasticity — Week 1: What's the Matter?"
date: 2026-08-29
tags:
  - Theory of Elasticity
related_posts: false
---

### Two descriptions of a solid

| | Discrete (microscopic) | Continuum (macroscopic) |
|---|---|---|
| Object | $N \sim 10^{23}$ atoms per $\text{cm}^3$ on a lattice, spacing $a \sim 10^{-10}\,\text{m}$ | Smooth fields: $\rho$, $\mathbf{u}$, $\boldsymbol{\varepsilon}$, $\boldsymbol{\sigma}$ |
| Interaction | Interatomic potentials; disturbances travel as phonons | PDEs from calculus |
| Used for | Nanostructures, crack-tip cores | Beams, plates, shafts, soil, bone |

Elasticity is written entirely in the continuum language.

### What elasticity computes

Given geometry, material, and boundary conditions (loads, supports), at every point of the body:

- **Displacement** $\mathbf{u}(\mathbf{x})$
- **Strain** $\boldsymbol{\varepsilon}(\mathbf{x})$ it produces
- **Stress** $\boldsymbol{\sigma}(\mathbf{x})$ needed to sustain it, and whether it exceeds what the material can bear

These must be smooth and differentiable, which requires the continuum assumption.

### The continuum hypothesis

Matter is discrete, but every point is assigned a smooth field value as if matter filled space without gaps. Density at a point:

$$
\rho(\mathbf{x}) = \lim_{\Delta V \to 0} \frac{\Delta m}{\Delta V}
$$

Taken literally this limit fails: at atomic size, $\Delta m/\Delta V$ jumps between nucleus and empty space. It is saved by a **window of scales**:

$$
a^3 \;\ll\; \delta V' \;\ll\; L^3
$$

- $a$: atomic spacing
- $\delta V'$: volume with enough atoms for a stable average, the **representative volume element (RVE)**, i.e. a "material point"
- $L$: size of the structure

**Knudsen-type criterion:** $\text{Kn} = \lambda / L$, with $\lambda$ the microscopic length (lattice spacing or grain size). Continuum theory holds for $\text{Kn} \ll 1$.

| Scale | Order of magnitude |
|---|---|
| Atomic spacing $a$ | $\sim 10^{-10}\,\text{m}$ |
| RVE size $\delta V'^{1/3}$ | $\sim 10^{-8}\text{–}10^{-6}\,\text{m}$ |
| Engineering length $L$ | $\sim 10^{-3}\text{–}10^{1}\,\text{m}$ |

### Where it holds and where it fails

| Holds ($\text{Kn} \ll 1$) | Fails ($\text{Kn} \to 1$) |
|---|---|
| Steel beam, $L \sim 1\,\text{m}$ | Nanowire a few lattice spacings wide |
| Concrete dam | Few-layer graphene film |
| Polycrystal with grains $\ll$ part size | Polycrystal with grain size $\approx$ feature size |
| Quasi-static loading | Shock / very high strain rate (no local equilibrium to average over) |
| Stress away from a crack | Crack-tip: continuum stress is singular, so cohesive-zone or atomistic models are needed |

### Recap

| Question | Answer |
|---|---|
| What is matter? | Discrete atoms bound by interatomic forces |
| Why does it matter? | Elasticity predicts deformation, stress, and failure of load-bearing solids |
| Continuous or discrete? | Discrete in reality, continuous by modeling choice; valid when $a \ll \delta V'^{1/3} \ll L$ |
