---
layout: post
title: "Modeling and Analysis of Mechanical Systems — Chapter 1: Classify and Analyze"
date: 2026-08-29
tags:
  - Modeling and Analysis of Mechanical Systems
related_posts: false
---

Before trying to solve any equation, it helps to be able to look at it and immediately place it: where did it come from, what mathematical species is it, and what extra information does it need to pin down one specific solution? This post builds that vocabulary from the ground up, then works two full examples end to end.

### 1. What is a model?

Mechanical members and components — gears, beams, springs, shafts, trusses, and so on — assemble into a **mechanical system**. These systems do three things: convert motion, support loads, and transfer or store energy (of some kind).

A **model** is the mathematical description of a mechanical system or a mechanical process. Models split into two families, depending on where they come from:

- **Physics-based models** are built from conservation principles (conservation of mass, energy, momentum) together with constitutive equations — relationships found experimentally and valid across a range of cases (Hooke's law, Fourier's law, and the like). Every term in the model traces back to a physical mechanism we could point to.
- **Data-driven models** fit a mathematical structure directly to measured data, with no reference to physical law at all — weather forecasting and large-scale simulation surrogates are the standard examples.

**We'll focus on physics-based models throughout** — that's the lens for everything that follows.

### 2. Three types of models

A physics-based model takes one of three mathematical forms, in increasing order of richness:

1. **Algebraic expressions** — no derivatives at all. Example: the static form of Hooke's law, $F = kx$, relating variables at a single instant with no rate of change involved.
2. **Ordinary differential equations (ODEs)** — the unknown depends on a single independent variable (usually time $t$), so its rate of change enters the model. Example: a lumped mass-spring oscillator,

   $$
   m\frac{d^2x}{dt^2} + kx = 0.
   $$

   Time derivatives show up constantly in mechanics, so it's standard to compress them using **Newton's dot notation**: one dot per derivative with respect to time, $\dot x = \frac{dx}{dt}$ and $\ddot x = \frac{d^2x}{dt^2}$. The equation above is then usually written $m\ddot x + kx = 0$ — same equation, just more compact. We'll use dot notation from here on.

3. **Partial differential equations (PDEs)** — the unknown depends on more than one independent variable at once (e.g., both position $x$ and time $t$). Example: a vibrating elastic rod,

   $$
   \rho A\, \frac{\partial^2 u}{\partial t^2} = EA\, \frac{\partial^2 u}{\partial x^2}.
   $$

   Partial derivatives get their own shorthand too — a subscript for each variable differentiated against: $u_t = \frac{\partial u}{\partial t}$, $u_{tt} = \frac{\partial^2 u}{\partial t^2}$, and likewise $u_{xx} = \frac{\partial^2 u}{\partial x^2}$. So the rod equation above is usually written $\rho A\, u_{tt} = EA\, u_{xx}$. We'll use subscript notation from here on too.

This progression tracks how many independent numbers it takes to pin down the system's state. An algebraic model is defined pointwise; a lumped ODE model needs a fixed, finite set of coordinates — its **degrees of freedom (DOF)**; a continuous PDE model needs a value at _every_ point in space, i.e., infinitely many DOF. So "discrete vs. continuous" is really the ODE/PDE distinction restated in terms of how many numbers we're tracking:

| System                                                       | Model type   | DOF      |
| ------------------------------------------------------------ | ------------ | -------- |
| Static spring under a known load                             | Algebraic    | —        |
| Single mass on a spring                                      | ODE          | 1        |
| Two masses connected by springs (in series)                  | Coupled ODEs | 2        |
| Cantilever beam (deflection at every point along its length) | PDE          | $\infty$ |

### 3. Four properties of a model

Once the model's type (algebraic/ODE/PDE) is settled, four further yes/no questions describe its character.

**Linear vs. non-linear.** The formal test is two conditions together, stated on a general input–output system:

- _Superposition (addition):_ if input $i_1$ produces response $r_1$, and input $i_2$ produces response $r_2$, then applying $i_1 + i_2$ together produces exactly $r_1 + r_2$.
- _Proportionality (scaling):_ scaling the input by a constant $k$ scales the output by the same $k$ — input $ki$ produces output $kr$.

{% include figure.liquid path="assets/img/meen602/fig-linear-system.svg" class="img-fluid rounded" %}

A system that satisfies both is linear; if either fails, it's non-linear. Quick test: every occurrence of the unknown and its derivatives must appear **alone, to the first power** — never raised to a power, never inside a nonlinear function ($\sin$, $\exp$, $\sqrt{\ \ }$, …), never multiplied by another unknown or by itself. Coefficients are free to depend on the _independent_ variables ($t$, $x$); linearity is a statement about the unknown only.

- _Linear:_
  - $m\ddot u + c\dot u + ku = 0$ — $u$, $\dot u$, $\ddot u$ each appear once, first power, constant coefficients.
  - $EI\,w'''' = q(x)$ — all $w$ terms are first power; the load $q(x)$ carries no $w$. (Non-homogeneous but still linear — the two properties are independent.)
  - $\ddot u + t^2 u = 0$ — the variable coefficient $t^2$ multiplies the _independent_ variable, not the unknown, so this stays linear.
  - $m_1\ddot x_1 + k(x_1 - x_2) = 0$ — coupled, but each unknown appears to the first power.

- _Non-linear_ (offending term named):
  - $I\ddot\theta + k\theta + \varepsilon\theta^3 = T_0\cos\Omega t$ — the $\theta^3$ term is a power of the unknown; it breaks proportionality.
  - $\ddot\theta + \dfrac{g}{L}\sin\theta = 0$ (exact pendulum) — $\sin\theta$ is a nonlinear function of the unknown.
  - $u_t + u\,u_x = \nu\,u_{xx}$ (Burgers' equation) — $u\,u_x$ is the unknown multiplied by its own derivative.
  - $\dot T = -\sigma\left(T^4 - T_\infty^4\right)$ (radiative cooling) — the $T^4$ term.

**Homogeneous vs. non-homogeneous.** _If every term contains the dependent variable or one of its derivatives, the equation is homogeneous._ The moment one isolated term appears that doesn't involve the dependent variable at all — a forcing, source, or load term — the equation is non-homogeneous. Quick test: move every term that carries the unknown to the left-hand side; the equation is homogeneous if and only if what's left on the right is exactly zero.

- _Homogeneous_ — every term carries the unknown:
  - $m\ddot u + c\dot u + ku = 0$ — each term has $u$, $\dot u$, or $\ddot u$.
  - $\dot\theta + \beta\theta = 0$ — both terms contain $\theta$.
  - $u_{tt} - c^2 u_{xx} = 0$ — both terms contain $u$.
  - $\ddot u + t^2 u = 0$ — still homogeneous: the variable coefficient $t^2$ is _attached to_ $u$, not standing alone.
  - $m_1\ddot x_1 + k(x_1 - x_2) = 0$ — $x_1$ and $x_2$ are both unknowns, so even a coupled system can be fully homogeneous.

- _Non-homogeneous_ — one term stands alone with no unknown in it:
  - $m\ddot u + ku = F_0\cos\Omega t$ — the forcing term $F_0\cos\Omega t$ has no $u$.
  - $EI\,w'''' = q(x)$ — the distributed load $q(x)$ depends only on position.
  - $u_{tt} - c^2 u_{xx} = g$ — the gravity term $g$ sits by itself.
  - $\dot T + \beta T = \beta T_\infty$ — the constant $\beta T_\infty$ carries no $T$. This is exactly the cooling equation of §5; the substitution $\theta = T - T_\infty$ absorbs that constant and _converts_ it to the homogeneous $\dot\theta + \beta\theta = 0$ — a change of variable can move an equation between the two categories.

**Deterministic vs. probabilistic.** A **deterministic** model produces exactly the same output every time for the same input — no randomness anywhere in it. A **probabilistic (stochastic)** model has randomness or uncertainty built into it, so the same nominal input produces a distribution of possible outputs rather than one fixed answer. Every physics-based model built purely from conservation laws and constitutive relations, as we're using here, is deterministic by default.

**Single vs. coupled.** One equation with one unknown, versus a system of equations whose unknowns appear in each other's equations.

### 4. Auxiliary conditions: IVP vs. BVP

Pinning down one specific solution out of an ODE or PDE's general solution requires **auxiliary conditions** — as many of them as the equation's order. There are two kinds:

- **Initial value problem (IVP):** all auxiliary conditions are specified at a single value of the independent variable (e.g., everything given at $t=0$).
- **Boundary value problem (BVP):** auxiliary conditions are specified at more than one value of the independent variable (e.g., one condition at $x=0$, another at $x=L$). Because a BVP needs conditions imposed at two separated points, the underlying equation must be **second order or higher** — a first-order ODE only carries one constant of integration, which isn't enough to independently satisfy conditions at two different points in general.

### 5. Worked example — transient cooling of a lumped mass

A small body at uniform temperature $T=T_i$ at $t=0$ is exposed to a surrounding fluid at $T_\infty$. Assume the body is small/conductive enough that its temperature is spatially uniform at every instant (the _lumped-capacitance_ assumption) — so $T=T(t)$ alone, with no spatial dependence, which is exactly why this is an ODE model rather than a PDE one. Find $T(t)$.

{% include figure.liquid path="assets/img/meen602/fig-lumped-cooling.svg" class="img-fluid rounded" %}

**Set up the physics — conservation of energy.**

$$
E_{in} + E_{gen} = E_{stored} + E_{loss}.
$$

With no generation and no input other than the initial temperature itself, $E_{stored} + E_{loss} = 0$, i.e., rate of change of internal energy plus rate of heat loss to the surroundings equals zero:

$$
\frac{d}{dt}\left(mc_v T\right) + hA\left(T - T_\infty\right) = 0.
$$

**Reduce to a standard ODE.** With $m$, $c_v$ constant,

$$
\frac{dT}{dt} = -\frac{hA}{mc_v}\left(T-T_\infty\right) = -\beta\left(T-T_\infty\right), \qquad \beta = \frac{hA}{mc_v},
$$

with auxiliary condition $t=0,\ T=T_i$ — a single point, so this is an **IVP**.

**Solve it.** Substitute $\theta = T - T_\infty$, so $\dot\theta = \dot T$:

$$
\dot\theta = -\beta\theta \quad\Leftarrow\ \text{homogeneous (every term contains } \theta \text{ or its derivative)}.
$$

Separating variables, $\int \frac{d\theta}{\theta} = -\beta \int dt \ \Rightarrow\ \ln\theta = -\beta t + C \ \Rightarrow\ \theta = Ce^{-\beta t}$. Applying the initial condition ($t=0,\ \theta = T_i - T_\infty$) gives $C = T_i - T_\infty$, so

$$
\boxed{T(t) = T_\infty + \left(T_i - T_\infty\right)e^{-\beta t}.}
$$

**Classification of this model:** physics-based (from conservation of energy), an ODE (lumped — $T$ depends on $t$ alone), linear, homogeneous (once shifted to $\theta$), deterministic, single, and an IVP.

### 6. Worked example — spring-mass free vibration

A mass $m$ on a spring of stiffness $k$, displacement $u(t)$, no damping or forcing:

{% include figure.liquid path="assets/img/meen602/fig-spring-mass.svg" class="img-fluid rounded" %}

$$
m\ddot u + ku = 0, \qquad u(0) = u_0,\ \dot u(0) = 0 \quad (\text{an IVP}).
$$

**Classification:** physics-based (Newton's second law), an ODE, linear, homogeneous (both terms contain $u$ or its derivative), deterministic, single.

Because it's linear and homogeneous, the full solution is just the complementary function — no particular integral needed. Solving via the characteristic equation gives

$$
u(t) = C_1\cos(\omega_n t) + C_2\sin(\omega_n t), \qquad \omega_n = \sqrt{\frac{k}{m}}.
$$

Applying the initial conditions: $u(0)=C_1=u_0$, and $\dot u(0) = C_2\omega_n = 0 \Rightarrow C_2 = 0$, so

$$
u(t) = u_0\cos(\omega_n t),
$$

i.e., free vibration at the system's natural frequency $\omega_n$.

### 7. From ODEs to numerical solutions

Two facts bridge directly into solving these equations numerically:

- **Order reduction.** Any $n$th-order ODE can be rewritten as a system of $n$ first-order ODEs by introducing $(n-1)$ new variables (e.g., letting $v=\dot u$ turns the spring-mass equation above into two coupled first-order equations for $u$ and $v$). This is the standard trick that lets a single numerical time-stepping scheme handle an ODE of any order.
- **Euler's method**, derived by integrating $\frac{dy}{dx}=f(x,y)$ over one step $[x_0, x_0+h]$:

$$
\int_{y_0}^{y_0+h} dy = \int_{x_0}^{x_0+h} f(x,y)\,dx.
$$

- **Explicit (forward) Euler** evaluates the integrand at the known, left-hand point: $y_{0+h} = y_0 + h\,f(x_0, y_0)$.
- **Implicit (backward) Euler** evaluates it at the unknown, right-hand point instead: $y_{0+h} = y_0 + h\,f(x_0+h,\ y_{0+h})$ — which has to be solved for $y_{0+h}$ since it appears on both sides.

This is the starting point for solving ODEs numerically — the natural next step once an equation has been classified.

### 8. Classifying PDEs

PDEs get an extra classification layer on top of everything above — both a mathematical label and a physical one, and the two are tied together.

**Mathematical classification.** For the general second-order linear PDE in two independent variables, $A\,u_{xx} + B\,u_{xy} + C\,u_{yy} + (\text{lower-order terms}) = 0$, the discriminant $\Delta = B^2-4AC$ sorts it into one of three families:

| Discriminant | Type           | Canonical form                                   | Mechanical-system example                                                                  |
| ------------ | -------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| $\Delta < 0$ | **Elliptic**   | Laplace's equation, $\nabla^2 u = 0$             | Steady-state temperature or steady membrane/torsion deflection — no time dependence at all |
| $\Delta = 0$ | **Parabolic**  | Diffusion/heat equation, $u_t = \alpha\, u_{xx}$ | Transient (unsteady) heat conduction along a bar                                           |
| $\Delta > 0$ | **Hyperbolic** | Wave equation, $u_{tt} = c^2 u_{xx}$             | A vibrating string or elastic rod                                                          |

($\nabla^2$, the _Laplacian_, is itself a shorthand: $\nabla^2 u = u_{xx} + u_{yy}$ in two dimensions, the sum of the unmixed second partial derivatives — extending to $u_{xx}+u_{yy}+u_{zz}$ in three.)

**Reading off $A$, $B$, $C$.** Move everything to one side, then match the coefficients of the three second-order terms $u_{xx}$, $u_{xy}$, $u_{yy}$. The two independent variables can be any pair — $(x, y)$ for a spatial field, or $(x, t)$ when one of them is time. A missing second-order term just means its coefficient is zero.

- Laplace, $u_{xx} + u_{yy} = 0$: $A = 1,\ B = 0,\ C = 1$, so $\Delta = 0 - 4(1)(1) = -4 < 0$ → **elliptic**.
- Heat, $\alpha\,u_{xx} - u_t = 0$: the only second-order term is $u_{xx}$, so $A = \alpha,\ B = 0,\ C = 0$ (there is no $u_{tt}$), giving $\Delta = 0 - 4(\alpha)(0) = 0$ → **parabolic**.
- Wave, $c^2 u_{xx} - u_{tt} = 0$: $A = c^2,\ B = 0,\ C = -1$, so $\Delta = 0 - 4(c^2)(-1) = 4c^2 > 0$ → **hyperbolic**.

The sign of $\Delta$ is really counting how many real **characteristic directions** the equation has — curves in the $(x, t)$ plane along which information travels: **two** for hyperbolic, **one** for parabolic, **none** for elliptic.

**Physical classification.** Parabolic and hyperbolic equations are **marching (propagation) problems**: given the state everywhere at one instant, the solution advances forward step by step. Elliptic equations are **jury (equilibrium) problems**: there's no time-like variable to march along, so the interior solution depends simultaneously on the boundary data all the way around the domain — the whole domain has to be considered together, the way a jury weighs all the evidence at once. The lumped-cooling example above, run long enough that $dT/dt\to 0$, degenerates from a marching problem into an equilibrium one — a nice bridge between the two worlds.

**Propagation speed and the domain of dependence.** The number of characteristics above is the same thing as _how fast information travels_, and that fixes the **domain of dependence** of a point — the set of earlier/boundary data that can affect the solution there.

- **Hyperbolic — finite speed.** A disturbance moves at a fixed, finite speed $c$ along the two characteristics. The solution at $(x, t)$ depends only on the initial data in the interval $[x - ct,\ x + ct]$ — a _finite_ domain of dependence, the base of a backward-pointing wedge. Conversely, a disturbance made at one point can only reach the forward wedge $\vert x' - x \vert \le c\,(t' - t)$, its zone of influence. Outside that wedge nothing has changed yet, so wavefronts stay sharp.
- **Parabolic — infinite speed, but decaying.** There is only one characteristic direction ($t = \text{const}$), and the propagation speed is formally infinite: a change anywhere is felt _everywhere_ at the next instant. But the influence decays exponentially with distance, so the domain of dependence is the whole line at earlier times, weighted heavily toward nearby points — you see smooth smearing, not a clean front.
- **Elliptic — no propagation.** No time-like direction, no real characteristics, no wavefront. Every interior point depends on the _entire_ boundary at once, so its domain of dependence is the whole domain. That is precisely why elliptic problems are "jury" problems — there is no direction along which to march information in, so all the boundary evidence must be weighed simultaneously.

### Recap

| Topic                | Core question it answers                                                                                                                                                                                                         |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| What is a model      | Physics-based (conservation + constitutive laws) or data-driven — we'll focus on physics-based.                                                                                                                                  |
| Three model types    | Algebraic, ODE, or PDE — tracking finite DOF (discrete) vs. infinite DOF (continuous).                                                                                                                                           |
| Four properties      | Linear/non-linear (superposition + proportionality), homogeneous/non-homogeneous, deterministic/probabilistic, single/coupled.                                                                                                   |
| Auxiliary conditions | IVP (all conditions at one point) vs. BVP (conditions at ≥2 points, requiring order ≥ 2).                                                                                                                                        |
| Worked examples      | Lumped cooling and spring-mass free vibration — both physics-based, ODE, linear, homogeneous, deterministic, single, IVP.                                                                                                        |
| Numerical solutions  | Order reduction ($n$th-order → $n$ first-order ODEs) and Euler's method (explicit/implicit).                                                                                                                                     |
| PDE classification   | Elliptic/parabolic/hyperbolic by the sign of $\Delta = B^2 - 4AC$ (0, 1, or 2 real characteristics); marching (finite or infinite propagation speed) vs. jury (no propagation — whole-boundary domain of dependence) physically. |

### Check our understanding — Chapter 1 practice questions

Try each one before expanding the answer underneath it.

**Q1. Classify the model.** A gym installs pressure sensors in a treadmill belt and fits a regression model that predicts belt wear directly from millions of logged (speed, load, time) readings — with no reference to friction coefficients, belt stiffness, or any other physical parameter. Meanwhile, a colleague models the same belt's vibration using $m\ddot x + c\dot x + kx = F(t)$, where $m, c, k$ are measured mass, damping, and stiffness.

<ol type="a" markdown="1">
  <li>Which model is physics-based and which is data-driven, and what's the distinguishing test?</li>
  <li>The vibration model above is a single lumped mass. What is the DOF of this model, and how would the DOF change if we modeled the belt as a continuous, distributed-mass loop instead?</li>
  <li>Is the lumped vibration model an algebraic, ODE, or PDE model — and what would it become if we modeled the belt as a continuous loop instead?</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li>The regression model is <em>data-driven</em> — it fits input/output data directly with no physical parameter we could point to and measure independently; the vibration model is <em>physics-based</em> because $m$, $c$, $k$ each correspond to a measurable physical property and the equation comes from Newton's second law.</li>
  <li>The lumped model has <strong>1 DOF</strong> (a single coordinate $x$ describes the whole system); modeled as a continuous loop, the DOF becomes <strong>infinite</strong>, since displacement is now a field $u(s,t)$ defined at every point $s$ around the belt.</li>
  <li>It's an <strong>ODE</strong> model (derivatives in time only, finite DOF); modeled as a continuous loop it would become a <strong>PDE</strong> model, since displacement would then depend on both position $s$ and time $t$ at once.</li>
</ol>

</details>

---

**Q2. Analyze the mathematical properties.** A rotating shaft with a nonlinear stiffening spring is modeled as $I\ddot\theta + c\dot\theta + k\theta + \varepsilon\theta^3 = T_0\cos(\Omega t)$, with $\theta(0) = \theta_0$ and $\dot\theta(0) = 0$ given. Classify this equation fully:

<ol type="a" markdown="1">
  <li>Is it an algebraic, ODE, or PDE model?</li>
  <li>Linear or non-linear?</li>
  <li>Homogeneous or non-homogeneous?</li>
  <li>Deterministic or probabilistic?</li>
  <li>Single or coupled?</li>
  <li>IVP or BVP?</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li>An <strong>ODE</strong> — the only independent variable is $t$.</li>
  <li><strong>Non-linear</strong> — the $\theta^3$ term breaks both superposition and proportionality (the equation isn't a linear combination of $\theta$ and its derivatives alone).</li>
  <li><strong>Non-homogeneous</strong> — the $T_0\cos(\Omega t)$ term doesn't contain $\theta$ or any of its derivatives, so it's a forcing term.</li>
  <li><strong>Deterministic</strong> — every coefficient and input is a fixed value, so the same initial conditions always produce the same $\theta(t)$; there's no randomness built into the model.</li>
  <li><strong>Single</strong> — one equation, one unknown ($\theta$).</li>
  <li><strong>IVP</strong> — both conditions ($\theta_0$ and $\dot\theta(0)$) are specified at the same instant, $t=0$.</li>
</ol>

</details>

---

**Q3. Coupled systems and degrees of freedom.** Two railcars of mass $m_1$ and $m_2$ are connected by a coupler spring $k$ and each has its own damper to the ground, $c_1$ and $c_2$:

$$
m_1\ddot x_1 + c_1\dot x_1 + k(x_1-x_2) = 0, \qquad m_2\ddot x_2 + c_2\dot x_2 + k(x_2-x_1) = 0.
$$

<ol type="a" markdown="1">
  <li>How many degrees of freedom does this system have, and how can we tell just from the equations (without being told directly)?</li>
  <li>Is this system of equations single or coupled? Justify using the specific terms that make it so.</li>
  <li>If we were instead told the initial positions and velocities of both cars, what kind of problem (IVP or BVP) would this be, and why does that make physical sense for a <em>vibration</em> problem as opposed to a static deflection problem?</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li><strong>2 DOF</strong> — there are two independent coordinates, $x_1$ and $x_2$, each needing its own second-order equation.</li>
  <li><strong>Coupled</strong> — the term $k(x_1-x_2)$ in the first equation and $k(x_2-x_1)$ in the second means each railcar's equation contains the <em>other</em> car's coordinate, so the two equations can't be solved independently.</li>
  <li>It's an <strong>IVP</strong>, because all the given data ($x_1(0), \dot x_1(0), x_2(0), \dot x_2(0)$) is specified at a single instant, $t=0$ — vibration problems evolve forward in time from a known starting state, unlike static/BVP problems where conditions are imposed at separated spatial locations (like the two ends of a beam).</li>
</ol>

</details>

---

**Q4. Classify the PDEs.** Consider three field problems on a metal plate: (i) the temperature distribution once it has fully reached steady state, $\nabla^2 T = 0$; (ii) the temperature right after a hot iron touches the plate and heat is still spreading, $\partial T/\partial t = \alpha\nabla^2 T$; (iii) the out-of-plane deflection of the plate after being struck, $\partial^2 w/\partial t^2 = c^2\nabla^2 w$.

<ol type="a" markdown="1">
  <li>For each of the three PDEs, read off $A$, $B$, $C$, form the discriminant $\Delta = B^2 - 4AC$, and use its sign to state the mathematical classification (elliptic, parabolic, or hyperbolic) and the physical classification (marching/propagation vs. jury/equilibrium).</li>
  <li>Problems (ii) and (i) describe the <em>same physical plate</em>. Why does the governing PDE — and its classification — change between them?</li>
  <li>For problem (i), explain in our own words why it's called a "jury" problem: what has to be true about the boundary before we can find $T$ at a single interior point?</li>
  <li>Rank the three problems by how fast a localized disturbance propagates, and state what each solution's value at an interior point depends on — its domain of dependence.</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li>Put each equation in the form $A\,u_{xx} + B\,u_{xy} + C\,u_{yy} + \dots = 0$ and take one space dimension where needed so there are two independent variables:
    <ul markdown="1">
      <li>(i) $T_{xx} + T_{yy} = 0$: $A = 1,\ B = 0,\ C = 1$, so $\Delta = 0 - 4(1)(1) = -4 < 0$ → <strong>elliptic</strong>, a <strong>jury/equilibrium</strong> problem.</li>
      <li>(ii) $\alpha\,T_{xx} - T_t = 0$ — the only second-order term is $T_{xx}$, so $A = \alpha,\ B = 0,\ C = 0$, giving $\Delta = 0 - 4(\alpha)(0) = 0$ → <strong>parabolic</strong>, a <strong>marching/propagation</strong> problem (marches forward in time).</li>
      <li>(iii) $c^2 w_{xx} - w_{tt} = 0$: $A = c^2,\ B = 0,\ C = -1$, so $\Delta = 0 - 4(c^2)(-1) = 4c^2 > 0$ → <strong>hyperbolic</strong>, also a <strong>marching/propagation</strong> problem.</li>
    </ul>
  </li>
  <li>The physics changes from <em>transient</em> to <em>steady-state</em>: once $\partial T/\partial t \to 0$, the time-derivative term in the parabolic equation drops out, leaving only the spatial Laplacian — so $C$ goes from $0$ back to a nonzero value in the $(x,y)$ variables, $\Delta$ flips from $0$ to negative, and the equation degenerates from parabolic to elliptic. The marching (time-stepping) problem becomes an equilibrium problem.</li>
  <li>In an elliptic/jury problem the interior solution depends <strong>simultaneously</strong> on the boundary values everywhere around the domain — we need to know the temperature all along the entire boundary before we can determine $T$ at any single interior point, since there's no time-like direction to sweep through one section at a time; all the "evidence" (boundary data) has to be weighed together at once, as a jury would.</li>
  <li>Fastest to slowest "cleanness" of propagation:
    <ul markdown="1">
      <li>(iii) hyperbolic — <strong>finite speed $c$</strong>. The deflection at $(x, t)$ depends only on the initial data within distance $ct$ of $x$, i.e. the interval $[x - ct,\ x + ct]$ cut out by the two characteristics — a finite domain of dependence with a sharp wavefront.</li>
      <li>(ii) parabolic — <strong>infinite mathematical speed but exponentially decaying influence</strong>. $T$ at a point depends on the <em>entire</em> earlier temperature field, weighted toward nearby points; the disturbance is felt everywhere immediately but smears out smoothly with no front.</li>
      <li>(i) elliptic — <strong>no propagation</strong>. $T$ at an interior point depends on the <em>whole boundary</em> at once; there is no earlier state and no wavefront.</li>
    </ul>
  </li>
</ol>

</details>

---

**Q5. Putting it all together.** A guitar string of length $L$, fixed at both ends, is modeled by $u_{tt} = c^2 u_{xx}$ for $0 < x < L$, with $u(0,t) = u(L,t) = 0$ for all $t$, and initial pluck shape $u(x,0) = f(x)$, $u_t(x,0) = 0$.

<ol type="a" markdown="1">
  <li>Is this system discrete or continuous, and how many degrees of freedom does it have?</li>
  <li>Classify the equation as ODE/PDE, linear/non-linear, and homogeneous/non-homogeneous.</li>
  <li>This problem has conditions specified at $x=0$ and $x=L$ <em>and</em> at $t=0$ — is it purely an IVP, purely a BVP, or something else? Explain.</li>
  <li>Mathematically and physically classify the PDE itself.</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li><strong>Continuous</strong> — the string's displacement $u(x,t)$ is a field defined at every point $x$, so it has <strong>infinite DOF</strong>.</li>
  <li>It's a <strong>PDE</strong> (derivatives in both $x$ and $t$), <strong>linear</strong> (superposition of solutions holds — no products or powers of $u$ or its derivatives), and <strong>homogeneous</strong> (no forcing term appears; every term contains $u$ or a derivative of $u$).</li>
  <li>It's a <strong>mixed initial-boundary value problem</strong>: the two spatial conditions at $x=0, L$ make it a BVP <em>in space</em>, while the two conditions at $t=0$ make it an IVP <em>in time</em> — continuous vibrating-system problems typically need both simultaneously.</li>
  <li>It's the canonical <strong>hyperbolic</strong> equation (discriminant $B^2-4AC > 0$ for $u_{tt}-c^2u_{xx}=0$), and physically a <strong>marching (propagation)</strong> problem — the pluck shape at $t=0$ is advanced forward in time as a wave traveling at speed $c$.</li>
</ol>

</details>

---

**Q6. Auxiliary conditions and model character.** A structural engineer proposes two models for the same beam: (i) a deterministic finite-element model built from $E$, $I$, and the applied load, giving one deflection curve for one set of inputs; (ii) a Monte Carlo model that treats Young's modulus as a random variable (to capture manufacturing variability) and reports a distribution of possible deflection curves. Separately, she wants to solve the beam's governing ODE, $EI\,d^4w/dx^4 = q(x)$, using conditions at both ends of the beam, $w(0)=w(L)=0$.

<ol type="a" markdown="1">
  <li>Which model, (i) or (ii), is deterministic and which is probabilistic? What's the defining difference?</li>
  <li>Is the beam problem $EI\,w'''' = q(x)$ with $w(0)=w(L)=0$ an IVP or a BVP?</li>
  <li>Why must this kind of two-ended problem be governed by an ODE of order two or higher — what would go wrong if it were only first order?</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li>Model (i) is <strong>deterministic</strong> — the same $E$, $I$, and load always produce the same deflection curve, with no randomness anywhere in the model. Model (ii) is <strong>probabilistic (stochastic)</strong> — because Young's modulus is treated as a random variable, the same nominal inputs produce a distribution of possible outputs rather than one fixed answer.</li>
  <li>A <strong>BVP</strong> — the conditions are given at two separated values of the independent variable $x$ ($x=0$ and $x=L$), not both at a single point.</li>
  <li>A first-order ODE's general solution has only one arbitrary constant of integration, enough to satisfy exactly one auxiliary condition — imposing two independent conditions at separate points would generally overdetermine it, with no solution except by coincidence. An equation of order two or higher has two or more constants of integration, giving it enough freedom to satisfy conditions imposed at two separate points.</li>
</ol>

</details>
