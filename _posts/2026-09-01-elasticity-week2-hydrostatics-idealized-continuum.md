---
layout: post
title: "Theory of Elasticity — Week 2: Hydrostatics as an Idealized Introduction to Continuum Elasticity"
date: 2026-09-01
tags:
  - Theory of Elasticity
related_posts: false
---

Hydrostatics is the simplest continuum: a fluid at rest carries no shear, so the stress tensor collapses to one scalar, the pressure. The same pattern (equilibrium equation + constitutive law) carries over to solids once shear returns.

### 1. Governing equation

Equilibrium of any continuum (no acceleration):

$$
\nabla\cdot\boldsymbol\sigma + \rho\,\mathbf b = 0
$$

with $\boldsymbol\sigma$ the stress, $\rho$ the density, $\mathbf b$ the body force per unit mass.

**A fluid at rest cannot support shear; a solid can.** So for a static fluid

$$
\boldsymbol\sigma = -p\,\mathbf I
$$

With gravity only ($z$ up, $g>0$):

$$
\frac{\partial p}{\partial x} = \frac{\partial p}{\partial y} = 0, \qquad \frac{dp}{dz} = -\rho(z)\,g(z)
$$

Pressure depends on height only. This is $\sum F = m\ddot u$ with $\ddot u = 0$; keeping $\ddot u \neq 0$ gives Euler's equation.

### 2. Constitutive law: equation of state (EOS)

Equilibrium is material-independent. A **constitutive equation** relates a field to a deformation measure through a material constant, like a spring $F = kx$. For a fluid it is the EOS

$$
p = f(\rho, T;\ \text{material parameters})
$$

which plays the role Hooke's law plays for solids.

### 3. Mass conservation

For a closed parcel $M = \rho V$, $dM = 0$:

$$
V\,d\rho + \rho\,dV = 0 \quad\Longrightarrow\quad \boxed{\frac{d\rho}{\rho} = -\frac{dV}{V}}
$$

### 4. Bulk modulus

$$
K \equiv -V\frac{\partial p}{\partial V} = \rho\frac{\partial p}{\partial \rho}
$$

- Units of pressure; $K>0$ since $\partial p/\partial V<0$.
- Large $K$ means stiff against volume change.
- Specify what is held fixed: isothermal $K_T$ or adiabatic $K_S$.

**Example: ideal gas, isothermal.** $p = \rho R T$ gives $\partial p/\partial\rho\big\vert_T = RT$, so

$$
K_T = \rho R T = p
$$

**Example: ideal gas, adiabatic.** $p = C\rho^\gamma$ gives $\partial p/\partial\rho = \gamma p/\rho$, so

$$
K_S = \gamma p
$$

Fast compression leaves no time for heat to escape, so the gas is stiffer ($\gamma > 1$).

### 5. Equations of state

**Polytropic:** $p = C\rho^{\,n}$

| $n$                  | Process / limit                              | $K = \rho\,\partial p/\partial\rho$ |
| -------------------- | -------------------------------------------- | ----------------------------------- |
| $0$                  | Isobaric ($p$ independent of $\rho$)         | $0$                                 |
| $1$                  | Isothermal ideal gas, $C = RT$               | $p$                                 |
| $\gamma \approx 1.4$ | Adiabatic, diatomic gas ($\gamma = c_p/c_v$) | $\gamma p$                          |
| $\to\infty$          | Incompressible                               | $\to\infty$                         |

**Incompressible vs isochoric.**

- _Incompressible_ is a material property: no volume change under any pressure, so $K\to\infty$. Example: water at ordinary pressures.
- _Isochoric_ is a process: volume held constant for the duration. Example: heating a gas in a sealed rigid tank. The gas is still compressible, so it is isochoric but not incompressible.

**Van der Waals** (real gas, $n_{\text{mol}}$ = moles):

$$
\left(p + \frac{a\,n_{\text{mol}}^2}{V^2}\right)(V - n_{\text{mol}} b) = n_{\text{mol}} RT
$$

- $b>0$: molecular volume, so $V > n_{\text{mol}}b$ always.
- $a>0$: intermolecular attraction, which lowers $p$ below the ideal-gas value.

### 6. Gravity versus position

Uniform sphere: radius $R_E$, density $\rho_E$, surface gravity $g_0$. By the shell theorem only the mass enclosed within $r$ matters.

| Region               | $g(r)$                                                             | Behavior              |
| -------------------- | ------------------------------------------------------------------ | --------------------- |
| Inside, $r \le R_E$  | $\dfrac{GM(r)}{r^2} = \tfrac43\pi G\rho_E\, r = g_0\dfrac{r}{R_E}$ | Linear, $0$ at center |
| Outside, $r \ge R_E$ | $\dfrac{GM_E}{r^2} = g_0\left(\dfrac{R_E}{r}\right)^2$             | Inverse-square        |

- Branches match at $r = R_E$; $g$ peaks at $g_0$ on the surface.
- Height $z$ above surface: $g(z) = g_0\big(R_E/(R_E+z)\big)^2 \approx g_0(1 - 2z/R_E)$ for $z \ll R_E$.
- $g(0)=0$ is required by symmetry at the center.
- The real Earth has a dense core, so $g$ exceeds $g_0$ part-way down; the uniform model is the idealization.

### 7. Isothermal atmosphere with varying gravity

Combine $\dfrac{dp}{dz} = -\rho g$, the isothermal EOS $\rho = p/(RT)$, and the outside gravity profile:

$$
\frac{dp}{p} = -\frac{g_0 R_E^2}{RT}\frac{dz}{(R_E+z)^2}
$$

Integrating from $0$ to $z$:

$$
\boxed{p(z) = p_0\exp\!\left[-\frac{g_0 R_E\, z}{RT\,(R_E+z)}\right]}
$$

- For $z \ll R_E$: $p \approx p_0 e^{-g_0 z/(RT)}$, the usual barometric law.
- As $z\to\infty$ the exponent tends to the finite $-g_0R_E/(RT)$, so $p$ never reaches zero. This is unphysical: a real atmosphere stops being isothermal and hydrostatic at great height.

### 8. From particles to fluids to solids

Every model needs the same four ingredients.

|                         | Particles                         | Fluids                                                                                           | Solids                                                                                                          |
| ----------------------- | --------------------------------- | ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| **Balance equation**    | $\sum \mathbf F + m\mathbf g = 0$ | $-\nabla p + \rho\mathbf b = 0$, i.e. $\partial_x p = \partial_y p = 0,\ \partial_z p = -\rho g$ | $\nabla\cdot\boldsymbol\sigma + \rho\mathbf b = 0$                                                              |
| **Constitutive law**    | $F = k\,\Delta L$                 | $p = f(\rho, T)$                                                                                 | $\boldsymbol\sigma = K\,\text{tr}(\boldsymbol\varepsilon)\,\mathbf I + 2G\,\boldsymbol\varepsilon^{\text{dev}}$ |
| **Kinematics**          | $\Delta L = u_2 - u_1$            | $\Delta V = f(\Delta L)$                                                                         | $\boldsymbol\varepsilon = \tfrac12\left(\nabla\mathbf u + (\nabla\mathbf u)^T\right)$                           |
| **Boundary conditions** | Newton's third law, or no forces  | $p(z{=}0) = p_0 = 1\,\text{atm}$                                                                 | $\mathbf u$ prescribed (fixed) or $\mathbf t = \boldsymbol\sigma\mathbf n$ prescribed (loaded / free)           |

Fluids cannot support shear stress at rest; solids can.

**Solid balance equation, index form.** Sum over $j$ for each component $i$:

$$
\sum_{j=1}^{3}\frac{\partial\sigma_{ij}}{\partial x_j} + \rho b_i = 0
$$

Written out:

$$
\begin{aligned}
\frac{\partial\sigma_{11}}{\partial x_1} + \frac{\partial\sigma_{12}}{\partial x_2} + \frac{\partial\sigma_{13}}{\partial x_3} + \rho b_1 &= 0\\
\frac{\partial\sigma_{21}}{\partial x_1} + \frac{\partial\sigma_{22}}{\partial x_2} + \frac{\partial\sigma_{23}}{\partial x_3} + \rho b_2 &= 0\\
\frac{\partial\sigma_{31}}{\partial x_1} + \frac{\partial\sigma_{32}}{\partial x_2} + \frac{\partial\sigma_{33}}{\partial x_3} + \rho b_3 &= 0
\end{aligned}
$$

Coordinates are relabeled $(x,y,z)\to(x_1,x_2,x_3)$, so $\mathbf x = [x_1, x_2, x_3]^T$.

**Solid constitutive law.**

$$
\boldsymbol\sigma = \underbrace{K\,\text{tr}(\boldsymbol\varepsilon)\,\mathbf I}_{\text{volume change}} + \underbrace{2G\,\boldsymbol\varepsilon^{\text{dev}}}_{\text{shape change}}, \qquad \boldsymbol\varepsilon^{\text{dev}} = \boldsymbol\varepsilon - \tfrac13\text{tr}(\boldsymbol\varepsilon)\,\mathbf I
$$

- $K$: bulk modulus. $G$: shear modulus. $\boldsymbol\varepsilon$: strain tensor.
- $\boldsymbol\varepsilon^{\text{dev}}$ (deviatoric strain) is the mathematical description of shape change.
- $\text{tr}(\boldsymbol\varepsilon) = \Delta V/V$ is the volume change.
- Fluid limit: $G = 0$ and $\boldsymbol\sigma = -p\mathbf I$, so $p = -K\,\text{tr}(\boldsymbol\varepsilon)$. This is Section 4's $K$ (compare $d\rho/\rho = -dV/V$).

**Boundary conditions for a solid.** On the boundary, either displacement or traction is specified, with traction

$$
\mathbf t = \boldsymbol\sigma\,\mathbf n
$$

where $\mathbf n$ is the outward unit normal.

| Condition                     | Meaning               | Example                                                  |
| ----------------------------- | --------------------- | -------------------------------------------------------- |
| $\mathbf u = 0$               | Fixed support         | Clamped left end of a bar                                |
| $\mathbf t = 0$               | Traction-free surface | Unloaded top, bottom, and free end                       |
| $\mathbf t = \bar{\mathbf t}$ | Applied load          | $t = -1000\,\text{MPa}$ pressing on a face (compressive) |

### Recap

| Quantity                 | Result                                                                                                                                                                                                                                                                                         |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Static fluid equilibrium | $dp/dz = -\rho g$ from $\boldsymbol\sigma = -p\mathbf I$                                                                                                                                                                                                                                       |
| Constitutive law         | EOS $p = f(\rho,T)$                                                                                                                                                                                                                                                                            |
| Mass conservation        | $d\rho/\rho = -dV/V$                                                                                                                                                                                                                                                                           |
| Bulk modulus             | $K = \rho\,\partial p/\partial\rho$; ideal gas: $K_T = p$, $K_S = \gamma p$                                                                                                                                                                                                                    |
| Gravity                  | $g_0 r/R_E$ inside, $g_0(R_E/r)^2$ outside                                                                                                                                                                                                                                                     |
| Atmosphere               | $p = p_0\exp[-g_0R_Ez/(RT(R_E+z))]$                                                                                                                                                                                                                                                            |
| Solid model              | $\nabla\cdot\boldsymbol\sigma+\rho\mathbf b=0$; $\boldsymbol\sigma = K\,\text{tr}(\boldsymbol\varepsilon)\mathbf I + 2G\boldsymbol\varepsilon^{\text{dev}}$; $\boldsymbol\varepsilon = \tfrac12(\nabla\mathbf u+\nabla\mathbf u^T)$; BCs $\mathbf u$ or $\mathbf t=\boldsymbol\sigma\mathbf n$ |

### Check Understanding

<details markdown="1">
<summary><strong>Q1.</strong> Why must $g(0)=0$ for any spherically symmetric body, and why does $g_0(1+r/R_E)$ fail as an interior formula?</summary>

Pull from opposite sides cancels at the center whatever the density profile, so $g(0)=0$. The formula $g_0(1+r/R_E)$ gives $g_0 \neq 0$ at $r=0$, so it is wrong.

</details>

<details markdown="1">
<summary><strong>Q2.</strong> Show that for an adiabatic ideal gas $K_S=\gamma p$.</summary>

$p = C\rho^\gamma \Rightarrow \partial p/\partial\rho = \gamma p/\rho \Rightarrow K_S = \rho\cdot\gamma p/\rho = \gamma p$.

</details>

<details markdown="1">
<summary><strong>Q3.</strong> Classify each as isochoric, incompressible, both, or neither: (a) water in a sealed rigid tank, (b) air heated in a sealed rigid tank, (c) air compressed in a piston, (d) water flowing through a pipe.</summary>

- (a) Both: constant volume, and the material is incompressible.
- (b) Isochoric only: constant-volume process, but air is compressible.
- (c) Neither: volume changes, and air is compressible.
- (d) Incompressible only: water is incompressible, but the flowing parcel is not a constant-volume process in a fixed container.

</details>

<details markdown="1">
<summary><strong>Q4.</strong> What happens to $p(z)$ as $z\to\infty$, and what does it say about the model?</summary>

$z/(R_E+z)\to1$, so $p\to p_0e^{-g_0R_E/RT} > 0$, a nonzero floor. A real atmosphere has finite mass; the isothermal and hydrostatic assumptions break down at high altitude.

</details>
