---
layout: post
title: "Theory of Elasticity — Week 3: A Solid from Atoms with the Lennard-Jones Potential"
date: 2026-09-08
tags:
  - Theory of Elasticity
related_posts: false
---

Build a solid from pairwise atomic bonds, then derive its pressure–volume law. The result shows why a solid, unlike a gas, can support **tensile pressure**.

### 1. Model: simple cubic lattice

Atoms sit at $\mathbf r = a\,(i,j,k)$ with integer $i,j,k$, where $a$ is the **lattice parameter**. The distance from the atom at the origin to atom $(i,j,k)$ is

$$
r_{ijk} = a\sqrt{i^2+j^2+k^2}
$$

- One atom per unit cell, so $V_{\text{cell}} = a^3$.
- $N$ = number of atoms = number of unit cells, so the system volume is $V = Na^3$.
- Example neighbors of the origin: $a(0,2,0)$ and $a(0,2,2)$, at distances $2a$ and $2\sqrt2\,a$.

### 2. Lennard-Jones potential

Bond energy between two atoms a distance $r$ apart:

$$
\phi(r) = 4\epsilon\left[\left(\frac{\sigma}{r}\right)^{12} - \left(\frac{\sigma}{r}\right)^{6}\right]
$$

| Term              | Role                                                                             |
| ----------------- | -------------------------------------------------------------------------------- |
| $(\sigma/r)^{12}$ | Repulsion, short-ranged and steep                                                |
| $(\sigma/r)^{6}$  | Attraction, longer-ranged                                                        |
| $\epsilon$        | Well depth: how strong the attraction is; loosely related to melting temperature |
| $\sigma$          | Distance where $\phi = 0$; only loosely related to atomic size                   |

{% include figure.liquid path="assets/img/elasticity/lj_pair_potential.png" class="img-fluid rounded z-depth-1" caption="Lennard-Jones pair potential: steep repulsion plus weaker, longer-ranged attraction." %}

Fitted values:

| Element | Atomic # | $\sigma$ (Å) | $\epsilon$ (eV) |
| ------- | -------- | ------------ | --------------- |
| He      | 2        | 2.56         | 0.00088         |
| Fe      | 26       | 2.32         | 0.401           |
| Ni      | 28       | 2.55         | 0.245           |
| Cu      | 29       | 2.38         | 0.415           |

**Example: Fe, nearest-neighbor-diagonal bond.** Atoms $(0,0,0)$ and $(0,-1,1)$ are $a\sqrt2$ apart. Take $a = 2\sigma$, so $r = 2\sqrt2\,\sigma$ and $(2\sqrt2)^6 = 512$:

$$
\phi = 4\epsilon\left[\frac{1}{(2\sqrt2)^{12}} - \frac{1}{(2\sqrt2)^{6}}\right]
= 4(0.401)\,\frac{1}{512}\left(\frac{1}{512}-1\right) \approx -3.13\times10^{-3}\,\text{eV} \approx -7.8\times10^{-3}\,\epsilon
$$

Bonds this long are very weak compared with $\epsilon$.

### 3. Energy per atom

Sum the bond energy of the atom at the origin with every other atom. The $\tfrac12$ avoids counting each bond twice:

$$
U = \frac12\sum_{\substack{i,j,k=-\infty\\(i,j,k)\neq(0,0,0)}}^{\infty}\phi(r_{ijk})
= 2\epsilon\left[\left(\frac{\sigma}{a}\right)^{12}\!\!\sum\frac{1}{(i^2+j^2+k^2)^6} - \left(\frac{\sigma}{a}\right)^{6}\!\!\sum\frac{1}{(i^2+j^2+k^2)^3}\right]
$$

$$
\boxed{U(a) = 2\epsilon\left[S_{12}\left(\frac{\sigma}{a}\right)^{12} - S_6\left(\frac{\sigma}{a}\right)^{6}\right]}, \qquad S_{12} \approx 6.2021,\quad S_6 \approx 8.4019
$$

$S_{12}, S_6$ are lattice sums over all integer triples except the origin. They depend only on the lattice geometry, and the $r^{-12}$ sum converges much faster than the $r^{-6}$ sum.

{% include figure.liquid path="assets/img/elasticity/lj_energy_per_atom.png" class="img-fluid rounded z-depth-1" caption="Energy per atom versus lattice parameter. The minimum sets the equilibrium spacing $a_0$." %}

Total energy of $N$ atoms: $U_{\text{sys}} = N\,U(a)$.

### 4. Pressure

$V = Na^3$, so $a = (V/N)^{1/3}$ and

$$
P = -\frac{\partial U_{\text{sys}}}{\partial V} = -\frac{\partial\,(N U)}{\partial\,(N a^3)} = -\frac{1}{3a^2}\frac{dU}{da}
$$

With $\dfrac{dU}{da} = 2\epsilon\left[-\dfrac{12\,S_{12}\sigma^{12}}{a^{13}} + \dfrac{6\,S_6\sigma^6}{a^{7}}\right]$:

$$
P = 4\epsilon\left[\frac{2S_{12}\sigma^{12}}{a^{15}} - \frac{S_6\sigma^{6}}{a^{9}}\right]
$$

Substituting $a^{15} = V^5/N^5$ and $a^{9} = V^3/N^3$:

$$
\boxed{P(V) = 4\epsilon\left[\frac{2S_{12}N^5\sigma^{12}}{V^5} - \frac{S_6N^3\sigma^{6}}{V^3}\right]}
$$

- Small $V$: the $V^{-5}$ repulsion dominates, $P>0$ (compression).
- Large $V$: the $V^{-3}$ attraction dominates, $P<0$ (tension).

### 5. Key points on the $P$–$V$ curve

| Point                                  | Condition   | Result                                                                 |
| -------------------------------------- | ----------- | ---------------------------------------------------------------------- |
| Equilibrium (unloaded)                 | $P = 0$     | $a_0 = \sigma\,(2S_{12}/S_6)^{1/6} \approx 1.067\,\sigma$              |
| Binding energy per atom                | $U(a_0)$    | $-S_6^2\epsilon/(2S_{12}) \approx -5.69\,\epsilon$                     |
| Maximum tension (theoretical strength) | $dP/da = 0$ | $a \approx 1.162\,\sigma$, $P_{\min} \approx -3.48\,\epsilon/\sigma^3$ |

{% include figure.liquid path="assets/img/elasticity/lj_pressure_volume.png" class="img-fluid rounded z-depth-1" caption="Pressure versus volume for the lattice. The shaded region is tensile pressure, which a gas cannot support. The gas curve is illustrative, with an arbitrary constant." %}

For Fe parameters, $a_0 \approx 2.48$ Å and $P_{\min} \approx -18$ GPa. Stretching past $a \approx 1.162\,\sigma$ makes the solid weaker, not stronger, which is the model's picture of failure.

**Equilibrium derivation.** $P = 0 \Leftrightarrow dU/da = 0$:

$$
\frac{12\,S_{12}\sigma^{12}}{a^{13}} = \frac{6\,S_6\sigma^{6}}{a^{7}}
\;\Longrightarrow\;
a^6 = \frac{2S_{12}}{S_6}\,\sigma^6
\;\Longrightarrow\;
a_0 = \sigma\left(\frac{2S_{12}}{S_6}\right)^{1/6} \approx 1.067\,\sigma
$$

So $a_0 \propto \sigma$: the equilibrium spacing is set by the potential, not chosen freely. Energy at equilibrium:

$$
U_{\text{sys}} = 2\epsilon N\left[S_{12}\left(\frac{\sigma}{a_0}\right)^{12} - S_6\left(\frac{\sigma}{a_0}\right)^{6}\right] = 2\epsilon N(-2.8456) \approx -5.69\,\epsilon N
$$

### 6. Deforming the lattice at fixed volume

Stretch the lattice by $\lambda$ along $x$ and shrink by $1/\sqrt\lambda$ along $y,z$:

$$
\mathbf r_{ijk} = a\left(\lambda\, i,\ \frac{j}{\sqrt\lambda},\ \frac{k}{\sqrt\lambda}\right), \qquad
|\mathbf r_{ijk}| = a\sqrt{\lambda^2 i^2 + \frac{j^2}{\lambda} + \frac{k^2}{\lambda}}
$$

- Volume is unchanged ($\lambda\cdot\lambda^{-1/2}\cdot\lambda^{-1/2} = 1$), so this changes shape only.
- $\lambda = 1$ is the cubic lattice, $\lambda>1$ is expansion along $x$, $\lambda<1$ is compression.
- $U$ keeps the same form, but the lattice sums now depend on $\lambda$:

$$
U = 2\epsilon\left[S_{12}(\lambda)\left(\frac{\sigma}{a}\right)^{12} - S_6(\lambda)\left(\frac{\sigma}{a}\right)^{6}\right], \qquad
S_n(\lambda) = \sum_{\substack{i,j,k\\(i,j,k)\neq 0}}\left(\lambda^2 i^2 + \frac{j^2+k^2}{\lambda}\right)^{-n/2}
$$

| $\lambda$           | $S_6(\lambda)$ | $S_{12}(\lambda)$ |
| ------------------- | -------------- | ----------------- |
| $1/2$ (compression) | 132.34         | 8194.16           |
| $1$ (cubic)         | 8.4019         | 6.2021            |
| $2$ (expansion)     | 37.696         | 260.10            |

Compression packs atoms closer along $x$, so the sums blow up and the repulsive $S_{12}$ term dominates.

{% include figure.liquid path="assets/img/elasticity/lj_energy_vs_lambda.png" class="img-fluid rounded z-depth-1" caption="Energy per atom at $a = a_0$ as the lattice is stretched ($\lambda>1$) or squeezed ($\lambda<1$) at constant volume. The cubic lattice is the minimum." %}

At $a = a_0$, $\lambda=1$ gives the lowest energy, $\approx -5.69\,\epsilon$ per atom. Either direction of this shape change raises it, so the cubic lattice is an energy minimum with respect to this deformation.

### 7. Solid vs gas

- **Ideal gas, isothermal:** $P \propto 1/V$. $P$ is always positive and has no equilibrium volume.
- **Lattice:** $P(V)$ crosses zero at $V_0 = Na_0^3$ and dips negative for $V>V_0$.

| System                      | Can it support $P<0$? | Why                                    |
| --------------------------- | --------------------- | -------------------------------------- |
| Ideal gas                   | No                    | No bonds to resist expansion           |
| Liquid, short of cavitation | Briefly               | Attraction between neighbors           |
| Solid                       | Yes                   | Bonded lattice resists being stretched |

This is the continuum link: near $V_0$, $P$ is approximately linear in $V - V_0$ with slope set by $K = -V\,dP/dV$ (Week 2), the bulk modulus.

### 8. Shape change: fluids vs solids

Take a cube of side $a$ (state $S_0$, undeformed) and stretch it to $2a \times a/\sqrt2 \times a/\sqrt2$ (state $S_t$, current). The cell volume stays $a^3$, so $V_0 = V_t$: a **volume-preserving (isochoric)** deformation, i.e. pure shape change. This is the $\lambda = 2$ lattice of Section 6.

|       | Energy cost of this shape change | Reason                                                  |
| ----- | -------------------------------- | ------------------------------------------------------- |
| Fluid | None: $U_t = U_0$                | No resistance to pure shape change (no shear stiffness) |
| Solid | Large: $U_t \gg U_0$             | Bonds are stretched and compressed                      |

Check with the Lennard-Jones lattice at $a = a_0$: $U_0 \approx -5.69\,\epsilon$ per atom, but at $\lambda = 2$, $U_t \approx +188\,\epsilon$.

### 9. Spring energy, anharmonicity, thermal expansion

A linear spring stores $\tfrac12 k x^2$, with $x$ the change in length from the rest length $l_0$. Near $a_0$ the lattice behaves like this spring, with $k = d^2U/da^2$ at $a_0$.

{% include figure.liquid path="assets/img/elasticity/lj_vs_spring.png" class="img-fluid rounded z-depth-1" caption="Lennard-Jones energy versus its harmonic-spring approximation. They agree near $a_0$ only." %}

|                          | Harmonic spring                                          | Lennard-Jones                             |
| ------------------------ | -------------------------------------------------------- | ----------------------------------------- |
| Shape                    | Symmetric parabola                                       | Asymmetric: steep wall, long tail         |
| Compressing to $l \to 0$ | Finite energy (unphysical: matter collapsing to nothing) | Energy $\to\infty$ (repulsion forbids it) |
| Stretching far           | Energy grows without bound                               | Energy levels off at $0$ (bond breaks)    |
| Valid                    | Small $x$ only                                           | All $a$ in the model                      |

**Thermal expansion.** Raising the temperature gives atoms more energy, so they oscillate between a turning point $l_{\min}$ and a larger $l_{\max}$. Because the well is asymmetric, the midpoint $l_{eq}$ moves outward as $T$ rises. A symmetric spring would give no thermal expansion.

{% include figure.liquid path="assets/img/elasticity/lj_thermal_expansion.png" class="img-fluid rounded z-depth-1" caption="Higher-energy oscillations in an asymmetric well have a larger mean spacing, which is thermal expansion." %}

### 10. Incompressibility and Poisson's ratio

$$
\nu = -\frac{\varepsilon_{\text{lateral}}}{\varepsilon_{\text{axial}}}
$$

For an isochoric (volume-preserving) deformation, $\nu = \tfrac12$.

| Condition    | Statement                                                  |
| ------------ | ---------------------------------------------------------- |
| Exact        | $\det\mathbf F = 1$                                        |
| Small strain | $\text{tr}(\boldsymbol\varepsilon) = \varepsilon_{kk} = 0$ |

Why $\nu = \tfrac12$: for small strain, $\varepsilon_{\text{axial}} + 2\varepsilon_{\text{lateral}} = 0$, so $\varepsilon_{\text{lateral}} = -\tfrac12\varepsilon_{\text{axial}}$.

| Material | $\nu$         | Comment                                              |
| -------- | ------------- | ---------------------------------------------------- |
| Rubber   | $\approx 0.5$ | Nearly incompressible, so it satisfies the condition |
| Steel    | $\approx 0.3$ | Changes volume under axial load                      |
| Cork     | $\approx 0$   | Barely expands sideways when compressed              |

So $\nu = \tfrac12$ is the incompressible limit, not a universal value.

### 11. Deformation gradient and strain

The deformation gradient $\mathbf F$ maps undeformed to current vectors. Its columns are the images of the basis vectors $\mathbf e_1,\mathbf e_2,\mathbf e_3$.

**Volume-preserving stretch:**

$$
\mathbf F = \begin{bmatrix}\lambda & 0 & 0\\ 0 & 1/\sqrt\lambda & 0\\ 0 & 0 & 1/\sqrt\lambda\end{bmatrix}, \qquad \det\mathbf F = 1
$$

**Example: unit square sheared to a parallelogram.** $\mathbf e_1 \to (2,0,0)$ (base of length 2) and $\mathbf e_2 \to 3(\cos45^\circ,\sin45^\circ,0)$ (slanted side of length 3 at $45^\circ$), with $\mathbf e_3$ unchanged:

$$
\mathbf F = \begin{bmatrix}2 & 3/\sqrt2 & 0\\ 0 & 3/\sqrt2 & 0\\ 0 & 0 & 1\end{bmatrix}, \qquad \det\mathbf F = 3\sqrt2 \approx 4.24 \ne 1
$$

This deformation is not volume-preserving. The out-of-plane direction does not change.

**Counter-example (volume-preserving shear):**

$$
\mathbf F = \begin{bmatrix}1 & \gamma & 0\\ 0 & 1 & 0\\ 0 & 0 & 1\end{bmatrix}, \qquad \det\mathbf F = 1 \text{ for any } \gamma
$$

**Strain** (small deformation, with $\mathbf F = \mathbf I + \nabla\mathbf u$):

$$
\boldsymbol\varepsilon = \tfrac12\left(\mathbf F + \mathbf F^T\right) - \mathbf I, \qquad \varepsilon_{ij} = \tfrac12\left(u_{i,j} + u_{j,i}\right)
$$

**Caution:** $\text{tr}(\boldsymbol\varepsilon)=0$ follows from $\det\mathbf F=1$ only to first order in the strain. For the stretch above, $\text{tr}(\boldsymbol\varepsilon) = (\lambda-1) + 2(\lambda^{-1/2}-1) \approx \tfrac34(\lambda-1)^2$, which is small only for $\lambda$ near 1. At $\lambda=2$ it is $\approx 0.41$, so the small-strain formula is not valid for the $\lambda=2$ example of Section 8.

### Recap

| Quantity               | Result                                                                                                                        |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Pair potential         | $\phi = 4\epsilon[(\sigma/r)^{12} - (\sigma/r)^6]$                                                                            |
| Energy per atom        | $U = 2\epsilon[S_{12}(\sigma/a)^{12} - S_6(\sigma/a)^6]$                                                                      |
| Pressure               | $P = -\dfrac{1}{3a^2}\dfrac{dU}{da} = 4\epsilon\left[\dfrac{2S_{12}N^5\sigma^{12}}{V^5} - \dfrac{S_6N^3\sigma^6}{V^3}\right]$ |
| Equilibrium            | $a_0 \approx 1.067\,\sigma$                                                                                                   |
| Distinguishing feature | $P<0$ for $V>V_0$: solids support tension                                                                                     |
| Shape change           | Free in fluids ($U_t = U_0$), costly in solids ($U_t \gg U_0$)                                                                |
| Thermal expansion      | Asymmetric well: mean spacing grows with $T$                                                                                  |
| Incompressible         | $\det\mathbf F = 1$, $\nu = \tfrac12$                                                                                         |
| Strain                 | $\boldsymbol\varepsilon = \tfrac12(\mathbf F + \mathbf F^T) - \mathbf I$ (small deformation)                                  |

### Check Understanding

<details markdown="1">
<summary><strong>Q1.</strong> Find the equilibrium lattice parameter of the Lennard-Jones solid with Fe parameters ($\sigma = 2.32$ Å), and state which condition fixes it.</summary>

$P = 0$, equivalently $dU/da = 0$, gives $a_0 = \sigma(2S_{12}/S_6)^{1/6} = \sigma\,(2\cdot6.2021/8.4019)^{1/6} \approx 1.067\,\sigma \approx 2.48$ Å.

</details>

<details markdown="1">
<summary><strong>Q2.</strong> Why can the lattice have $P<0$ while an ideal gas cannot?</summary>

The lattice atoms are bonded: for $a>a_0$ the attractive term dominates and pulls the solid back, so equilibrium requires a tensile (negative) pressure. An ideal gas has no interactions, so $P\propto1/V$ stays positive at every volume.

</details>

<details markdown="1">
<summary><strong>Q3.</strong> Why does a perfectly harmonic spring give no thermal expansion, while the Lennard-Jones bond does?</summary>

A symmetric parabola $\tfrac12kx^2$ gives turning points equally far on both sides of $l_0$ at any energy, so the midpoint never moves. The Lennard-Jones well is steep on the compression side and shallow on the stretch side, so higher energy pushes the outer turning point out much further than the inner one, and the midpoint drifts to larger spacing.

</details>

<details markdown="1">
<summary><strong>Q4.</strong> Which of these deformation gradients preserve volume? (a) $\text{diag}(2,\,1/\sqrt2,\,1/\sqrt2)$, (b) $\text{diag}(2,1,1)$, (c) a simple shear with $F_{12}=0.5$ and unit diagonal, (d) $1.1\,\mathbf I$.</summary>

Volume is preserved when $\det\mathbf F = 1$.

- (a) $2\cdot\tfrac{1}{\sqrt2}\cdot\tfrac{1}{\sqrt2} = 1$: preserved.
- (b) $\det = 2$: volume doubles.
- (c) Upper-triangular with unit diagonal, so $\det=1$: preserved.
- (d) $\det = 1.1^3 = 1.331$: volume grows by 33%.

</details>

<details markdown="1">
<summary><strong>Q5.</strong> A bar under axial load has $\varepsilon_{\text{axial}} = 0.002$ and $\varepsilon_{\text{lateral}} = -0.0006$. Find $\nu$ and decide whether the deformation is volume-preserving.</summary>

$\nu = -(-0.0006)/0.002 = 0.3$. The volumetric strain is $\text{tr}(\boldsymbol\varepsilon) = 0.002 + 2(-0.0006) = 0.0008 \neq 0$, so the volume increases. Incompressible behavior would need $\nu = \tfrac12$.

</details>

<details markdown="1">
<summary><strong>Q6.</strong> Why does the isochoric stretch of Section 8 cost essentially no energy in a fluid but a large energy in a solid?</summary>

A fluid cannot support shear, so pure shape change at constant volume leaves its energy unchanged ($U_t=U_0$). In a solid the same deformation stretches and compresses bonds away from $a_0$ along different directions; at $\lambda=2$ the lattice energy rises from about $-5.69\,\epsilon$ to about $+188\,\epsilon$ per atom, so $U_t \gg U_0$.

</details>
