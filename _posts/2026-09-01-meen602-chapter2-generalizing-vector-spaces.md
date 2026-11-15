---
layout: post
title: "Modeling and Analysis of Mechanical Systems — Chapter 2: Generalizing Vector Spaces"
date: 2026-09-01
tags:
  - Modeling and Analysis of Mechanical Systems
related_posts: false
---

Chapter 1 gave us a way to classify an equation once we already had it written down. Chapters 2 and 3 back up one step further and ask what the *objects* inside those equations actually are — a state, a signal, a deflected shape — and build the machinery (vector spaces, norms, projections, orthogonal bases) needed to work with them precisely. This post covers the first piece of that machinery: what a vector really is, once we stop thinking of it as just "an arrow," and how a whole space of such objects — discrete or continuous — is built from a short list of rules.

### 1. From continuous systems to lumped ones — discretization revisited

Chapter 1 sorted models into algebraic, ODE, and PDE, and tied that to degrees of freedom (DOF): finite for a lumped ODE model, infinite for a continuous PDE model. **Discretization** is the bridge between the two: it replaces a continuous field, known at *every* point, with its values at a finite set of grid points — trading an infinite-DOF PDE model for a finite-DOF, approximate lumped model that a computer can actually solve. (It is easy to say this backwards — discretization does not "build" a continuous system out of a lumped one; it goes the other way, lumped-from-continuous, by sampling.)

The standard tool is the **central difference approximation**. For a function $T(x)$ sampled at points $x_{i-1}, x_i, x_{i+1}$ spaced $\Delta x$ apart, Taylor-expanding $T$ about $x_i$ in both directions and subtracting gives

$$
\frac{d^2T}{dx^2}\bigg|_{x_i} \approx \frac{T_{i+1} + T_{i-1} - 2T_i}{\Delta x^2}.
$$

Substitute an approximation like this into a PDE — e.g., the steady heat equation $\dfrac{d^2T}{dx^2} = 0$ — and every derivative becomes an algebraic combination of the finite set of nodal values $T_i$. The infinite-dimensional problem "find $T(x)$ everywhere" becomes the finite-dimensional problem "find the numbers $T_1, T_2, \dots, T_n$" — precisely trading a continuous vector (a function) for a discrete one (a tuple of numbers), which is the theme of this whole chapter.

**A note on first-order PDEs.** Chapter 1's elliptic/parabolic/hyperbolic trichotomy, via the discriminant $\Delta = B^2-4AC$, was built for *second-order* PDEs. A general **first-order** PDE doesn't have that discriminant to compute — but it always has real characteristic curves along which it reduces to an ODE (the same idea used to solve the advection equation $u_t + c\,u_x = 0$ by following $x - ct = \text{const}$). Having real characteristics is exactly the hyperbolic signature, so a scalar first-order PDE is always a propagation (marching) problem in character — it is standard shorthand to call this "hyperbolic," even though it doesn't come from evaluating a discriminant the way a second-order equation's classification does.

### 2. State variables and the state vector

A **state variable** is one of the minimum set of variables whose values at some time $t_0$, together with the system's inputs from $t_0$ onward, are enough to determine the system's behavior for all $t \ge t_0$. "Minimum" is doing the real work in that definition: any extra variable that is just a function of the others (or of time) isn't a new state variable, and dropping a genuinely independent one makes the description incomplete.

Take the simple pendulum: a point mass $m$ on a rigid, massless rod of length $L$. Summing moments about the pivot gives $mL^2\ddot\theta + mgL\sin\theta = 0$, which simplifies (the mass cancels) to

$$
\ddot\theta + \frac{g}{L}\sin\theta = 0.
$$

This is second order in $\theta$, so it takes **two** numbers, $\theta$ and $\dot\theta$, to pin down the pendulum's future motion — the angle alone isn't enough, since the same angle with the pendulum swinging one way versus the other leads to completely different futures. Define the state variables $x_1 = \theta$ and $x_2 = \dot\theta$, collected into a **state vector** $\vec x = \begin{bmatrix} x_1 \\\\ x_2\end{bmatrix}$. The single second-order equation becomes the first-order pair

$$
\dot x_1 = x_2, \qquad \dot x_2 = -\frac{g}{L}\sin x_1,
$$

which is exactly the order-reduction trick from Chapter 1 ($n$th-order ODE $\to$ $n$ first-order ODEs), now with the new variables given a name — state variables — and packaged as a single vector living in a two-dimensional **state space** (here also called *phase space*).

### 3. What a vector actually is

The everyday picture of a vector — an arrow with a length and a direction, or a column of numbers — is a special case of something more general. A **vector** is any object that belongs to a collection of similar objects that can be *added together* and *scaled* by numbers, with the result always landing back in the same collection. That closure is the entire content of the idea: it doesn't matter whether the object is an arrow, a list of numbers, or a function, only that "add two of them" and "scale one of them" both make sense and don't leave the collection.

A **linear combination** of vectors $\vec x$ and $\vec y$ is any expression $a\vec x + b\vec y$ built from scalar multiples added together; a sum of many vectors, $\sum_i \lambda_i \vec v_i$, is a linear combination of all of them at once. Nearly everything in this chapter — span, basis, orthogonalization — is a statement about which linear combinations are reachable and which representation of them is most useful.

### 4. Notation conventions

To keep scalars, vectors, and spaces visually distinct from here on:

- Lowercase italic letters ($a$, $b$, $\lambda$) denote **scalars** — ordinary real (or complex) numbers.
- An arrow over a lowercase letter ($\vec a$, $\vec x$) denotes a **vector**.
- Script capital letters ($\mathcal{V}$) denote a **vector space** (also called a *linear space*) — the whole collection a vector belongs to, together with its addition and scaling rules.

### 5. Generalizing "vector": tuples and functions

The point of this chapter's first learning outcome is that "vector" stretches to cover objects that don't look anything like arrows, as long as they obey the same rules. Two important generalizations:

- **Discrete vectors: $n$-tuples.** An $n$-tuple is a finite, *ordered* list of $n$ elements (most often real numbers), written $(v_1, v_2, \dots, v_n)$ or as a column. Order matters — $(1,2)$ and $(2,1)$ are different tuples — and repeated values are allowed, so $(3,3,1)$ is a perfectly good 3-tuple. Every column vector in $\mathbb{R}^n$, and every state vector from §2, is an $n$-tuple.
- **Continuous vectors: functions.** A function $f(x)$ can be added to another function and scaled by a constant, and the result is again a function — so a function is a vector too, just one with infinitely many "components" (its value at every point $x$, rather than at a finite list of indices). This is exactly the discretization idea from §1 run in reverse: sampling a function at $n$ points turns the infinite-dimensional function-vector into a finite-dimensional $n$-tuple-vector, which is why a PDE's continuous field and a finite-difference grid of numbers are two views of the same underlying object.

### 6. Vector spaces: the axioms

A **vector space** (or *linear space*) $\mathcal{V}$ over the real numbers is a set of vectors, together with a rule for adding two vectors and a rule for scaling a vector by a real number $\lambda$, such that the following hold for all $\vec a, \vec b, \vec c \in \mathcal{V}$ and all scalars $\lambda, \lambda_1, \lambda_2 \in \mathbb{R}$:

1. **Closure under addition:** $\vec a + \vec b \in \mathcal{V}$.
2. **Closure under scalar multiplication:** $\lambda \vec a \in \mathcal{V}$.
3. **Commutativity of addition:** $\vec a + \vec b = \vec b + \vec a$.
4. **Associativity of addition:** $(\vec a + \vec b) + \vec c = \vec a + (\vec b + \vec c)$.
5. **Additive identity (zero vector):** there exists $\vec 0 \in \mathcal{V}$ such that $\vec a + \vec 0 = \vec a$.
6. **Additive inverse:** for every $\vec a$ there exists $-\vec a \in \mathcal{V}$ such that $\vec a + (-\vec a) = \vec 0$.
7. **Associativity of scalar multiplication:** $\lambda_1(\lambda_2 \vec a) = (\lambda_1\lambda_2)\vec a$.
8. **Distributivity over vector addition:** $\lambda(\vec a + \vec b) = \lambda \vec a + \lambda \vec b$.
9. **Distributivity over scalar addition:** $(\lambda_1 + \lambda_2)\vec a = \lambda_1 \vec a + \lambda_2 \vec a$.
10. **Identity of scalar multiplication:** $1\cdot \vec a = \vec a$.

Two clarifications worth being precise about: the scalars $\lambda$ multiplying a vector are numbers, *not* elements of $\mathcal{V}$ itself — writing "$\lambda \in \mathcal{V}$" mixes up the space with the field of scalars it's built over. And multiplying by the zero *scalar* isn't a separate axiom; it's a direct consequence of the ten rules above, $0\cdot \vec a = \vec 0$ for every $\vec a \in \mathcal{V}$ (not to be confused with axiom 5, the existence of a zero *vector*).

- *Vector spaces:*
  - $\mathbb{R}^n$ with ordinary componentwise addition and scaling — the standard example, satisfies all ten rules directly.
  - $2\times 2$ real matrices under matrix addition and scalar multiplication — sums and scalar multiples of $2\times 2$ matrices are still $2\times 2$ matrices, and all ten rules carry over componentwise.
  - The set of all real-valued continuous functions on $[0,1]$ — the sum of two continuous functions is continuous, a scaled continuous function is continuous, and the zero function and additive inverses ($-f$) stay in the set.
  - The set of solutions to a *linear, homogeneous* ODE, e.g., $m\ddot u + ku = 0$ — if $u_1(t)$ and $u_2(t)$ both solve it, so does $c_1u_1 + c_2u_2$ for any constants $c_1, c_2$ (this is exactly the superposition property from Chapter 1's linearity test), so the solution set is closed under both operations.

- *Not vector spaces* (rule violated named):
  - Vectors in $\mathbb{R}^2$ with both components non-negative — closed under addition, but **fails closure under scalar multiplication**: scaling $(1,1)$ by $\lambda=-1$ gives $(-1,-1)$, which has left the set.
  - The set of solutions to a *non-homogeneous* linear ODE, e.g., $m\ddot u + ku = F_0$ — if $u_1$ and $u_2$ both solve it, their sum $u_1+u_2$ solves $m\ddot u + ku = 2F_0$, not the original equation, so **closure under addition fails**. (This is why Chapter 1 kept homogeneous and non-homogeneous carefully separate — only the homogeneous solution set is a vector space.)
  - The set of solutions to a *non-linear* ODE, e.g., the exact pendulum $\ddot\theta + \frac{g}{L}\sin\theta = 0$ — superposition doesn't hold ($\sin$ isn't additive), so a sum of two solutions generally isn't a solution: **closure under addition fails**.
  - Unit vectors in $\mathbb{R}^2$ (all $\vec a$ with $\lVert \vec a \rVert = 1$) — scaling by any $\lambda \ne \pm 1$ changes the length, so the result leaves the set: **fails closure under scalar multiplication**. (It also has no zero vector, violating rule 5 on its own.)

### 7. Basis vectors

{% include figure.liquid path="assets/img/meen602/fig-basis-span.svg" class="img-fluid rounded" %}

A **basis** of a vector space $\mathcal{V}$ is a set of vectors that is (a) **linearly independent** — no vector in the set can be written as a linear combination of the others — and (b) **spanning** — every vector in $\mathcal{V}$ can be written as *some* linear combination of the basis vectors. Both properties matter: independence keeps the basis from having redundant members, and spanning guarantees nothing in $\mathcal{V}$ is left out of reach.

- *Bases:*
  - The standard unit vectors $\vec i, \vec j, \vec k$ in $\mathbb{R}^3$ — linearly independent (none is a combination of the other two), and every $\vec a = a_1\vec i + a_2\vec j + a_3\vec k$, so they span $\mathbb{R}^3$ as well.
  - Sines and cosines, $\{1, \cos x, \sin x, \cos 2x, \sin 2x, \dots\}$, as the basis functions of a **Fourier series** — any sufficiently well-behaved periodic function can be written as a linear combination (weighted sum) of these.
  - Powers of $x$, $\{1, x, x^2, x^3, \dots\}$, as the basis functions of a **Taylor series** — any sufficiently smooth function can be written (locally) as a linear combination of these.

- *Not bases* (which property fails):
  - $\{\vec i, \vec j, 2\vec i\}$ in $\mathbb{R}^2$ — spans $\mathbb{R}^2$, but $2\vec i$ is a linear combination of $\vec i$ alone, so the set **fails independence**; it's a spanning set, just not a *minimal* one.
  - $\{\vec i, \vec j\}$ treated as a claimed basis of $\mathbb{R}^3$ — linearly independent, but $\vec k$ (or any vector with a nonzero third component) can't be written using only $\vec i$ and $\vec j$, so it **fails to span**.
  - $\{1, \cos^2 x\}$ as a claimed basis for constants and $\cos 2x$ together — looks like two functions, but since $\cos 2x = 2\cos^2 x - 1$, this pair can only reach a one-dimensional slice of the space it's meant to span; it isn't independent enough (nor rich enough) to serve as a general Fourier-type basis.

### 8. Vector span

{% include figure.liquid path="assets/img/meen602/fig-vector-ops.svg" class="img-fluid rounded" %}

The **span** of a set of vectors is the set of *all* vectors reachable by taking linear combinations of them — every possible choice of scalars, all at once. For a single vector $\vec a = \begin{bmatrix}2\\\\3\end{bmatrix}$,

$$
\operatorname{span}(\vec a) = \left\{\lambda \vec a : \lambda \in \mathbb{R}\right\} = \left\{\lambda\begin{bmatrix}2\\3\end{bmatrix} : \lambda \in \mathbb{R}\right\},
$$

which traces out the entire line through the origin in the direction of $\vec a$. Because $\lambda = 0$ is an allowed choice, the **zero vector is always part of any span** — every span contains the origin.

Two independent vectors reach further. For $\vec a = \begin{bmatrix}1\\\\0\end{bmatrix}$ and $\vec b = \begin{bmatrix}0\\\\1\end{bmatrix}$,

$$
\operatorname{span}(\vec a, \vec b) = \left\{\lambda_1\vec a + \lambda_2 \vec b\right\} = \left\{\begin{bmatrix}\lambda_1\\\lambda_2\end{bmatrix} : \lambda_1, \lambda_2 \in \mathbb{R}\right\},
$$

and since $\lambda_1, \lambda_2$ can be *any* pair of real numbers, this reaches every point of the plane — $\operatorname{span}(\vec a, \vec b) = \mathbb{R}^2$. The same pattern extends to three dimensions: with $\vec i = [1,0,0]^T$, $\vec j = [0,1,0]^T$, $\vec k = [0,0,1]^T$, any vector in $\mathbb{R}^3$ is

$$
\vec a = \lambda_1 \vec i + \lambda_2 \vec j + \lambda_3 \vec k,
$$

so $\vec i, \vec j, \vec k$ both span $\mathbb{R}^3$ *and* (from §7) form a basis for it — span tells you what's reachable, and a basis is a minimal spanning set.

The **magnitude** (or *norm*) of a vector, written $\lvert \vec a \rvert$ or $\lVert \vec a \rVert$, measures how "big" it is — this quantity, and how it generalizes into inner products and dot products for comparing vectors, is the subject the next post picks up.

### Recap

| Topic | Core question it answers |
|---|---|
| Discretization | Turns a continuous, infinite-DOF field into a finite set of nodal values — sampling, not the reverse. |
| First-order PDEs | Always have real characteristics, so they behave as propagation (hyperbolic-type) problems even without a discriminant to compute. |
| State variables | The minimum set of variables (packaged as a state vector) whose initial values plus future inputs fix the system's future. |
| What a vector is | Any object in a collection closed under addition and scalar multiplication — not just an arrow. |
| Discrete vs. continuous vectors | $n$-tuples (finite, ordered, duplicates allowed) vs. functions (infinite-dimensional vectors). |
| Vector space axioms | Ten rules (closure, commutativity, associativity, identities, inverses, distributivity) a set must satisfy to be a linear space. |
| Basis | Linearly independent **and** spanning — minimal enough, but still reaches everything. |
| Span | Every vector reachable by linear combination; always contains the zero vector. |

### Check our understanding — Chapter 2 practice questions (Part 1)

Try each one before expanding the answer underneath it.

**Q1. Discretization direction.** A colleague says, "I built my continuous temperature field by discretizing my lumped nodal model." What's wrong with that sentence, and how would you correctly describe the relationship between a continuous heat-conduction PDE and a finite-difference grid of temperatures?

<details markdown="1">
<summary><strong>View Answer</strong></summary>

The direction is backwards. Discretization starts from a **continuous** model (a PDE, infinite DOF — a temperature defined at every point) and produces an **approximate, finite-DOF lumped model** by sampling it at a finite set of grid points and replacing derivatives with finite-difference formulas, e.g. $\frac{d^2T}{dx^2}\big|_{x_i} \approx \frac{T_{i+1}+T_{i-1}-2T_i}{\Delta x^2}$. A lumped nodal model doesn't "build" the continuous field — it's a finite approximation *of* it, and refining the grid (more nodes, smaller $\Delta x$) is what makes the lumped model converge back toward the true continuous solution.

</details>

---

**Q2. State variables for a spring-mass-damper.** A damped spring-mass system is governed by $m\ddot x + c\dot x + kx = 0$.

<ol type="a" markdown="1">
  <li>How many state variables does this system need, and why exactly that many?</li>
  <li>Define a state vector and rewrite the second-order equation as a first-order system.</li>
  <li>Is position $x$ alone a valid single state variable for this system? Why or why not?</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li><strong>Two.</strong> The equation is second order, so its general solution carries two arbitrary constants — equivalently, knowing $x$ alone at one instant doesn't tell you where the mass is heading, since the same position could be reached while moving in either direction (or at any speed). Position and velocity together are the minimum set that fixes the future motion.</li>
  <li>Let $x_1 = x$, $x_2 = \dot x$, so $\vec x = \begin{bmatrix}x_1\\x_2\end{bmatrix}$. Then $\dot x_1 = x_2$ and $\dot x_2 = -\frac{k}{m}x_1 - \frac{c}{m}x_2$ (solving the original equation for $\ddot x$ and substituting).</li>
  <li><strong>No</strong> — $x$ alone is not a complete state; it fails the "minimum set that determines the future" test because velocity is an independent piece of information not recoverable from position at a single instant.</li>
</ol>

</details>

---

**Q3. Testing the vector space axioms.** Consider the set $S$ of all $2\times 2$ matrices with determinant equal to zero, under ordinary matrix addition and scalar multiplication.

<ol type="a" markdown="1">
  <li>Is $S$ closed under scalar multiplication? Justify.</li>
  <li>Is $S$ closed under addition? Give a specific counterexample if not.</li>
  <li>Is $S$ a vector space?</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li><strong>Yes.</strong> If $\det(A) = 0$, then $\det(\lambda A) = \lambda^2 \det(A) = 0$ for any scalar $\lambda$, so $\lambda A$ still has zero determinant and stays in $S$.</li>
  <li><strong>No.</strong> Take $A = \begin{bmatrix}1&0\\0&0\end{bmatrix}$ and $B = \begin{bmatrix}0&0\\0&1\end{bmatrix}$ — both have $\det = 0$, so both are in $S$. But $A + B = \begin{bmatrix}1&0\\0&1\end{bmatrix}$ has determinant $1 \ne 0$, so $A+B \notin S$: closure under addition fails.</li>
  <li><strong>No.</strong> Failing even one of the ten axioms — here, closure under addition — disqualifies $S$ from being a vector space, regardless of how many of the other rules it satisfies.</li>
</ol>

</details>

---

**Q4. Basis or not?** In $\mathbb{R}^3$, consider the three vectors $\vec u_1 = [1,1,0]^T$, $\vec u_2 = [0,1,1]^T$, $\vec u_3 = [1,2,1]^T$.

<ol type="a" markdown="1">
  <li>Show that $\vec u_3$ is a linear combination of $\vec u_1$ and $\vec u_2$.</li>
  <li>Do $\{\vec u_1, \vec u_2, \vec u_3\}$ form a basis for $\mathbb{R}^3$? Which of the two basis requirements fails, and what does that tell you about $\operatorname{span}(\vec u_1, \vec u_2, \vec u_3)$?</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li>$\vec u_1 + \vec u_2 = [1,1,0]^T + [0,1,1]^T = [1,2,1]^T = \vec u_3$, so $\vec u_3 = 1\cdot\vec u_1 + 1\cdot\vec u_2$.</li>
  <li><strong>No.</strong> Because $\vec u_3$ is a combination of the other two, the set <strong>fails linear independence</strong> (part (a) of the basis test in §7) — it isn't a minimal spanning set. Consequently $\operatorname{span}(\vec u_1,\vec u_2,\vec u_3)$ is only the two-dimensional plane spanned by $\vec u_1$ and $\vec u_2$ alone (adding a dependent vector never extends the span), not all of $\mathbb{R}^3$.</li>
</ol>

</details>

---

**Q5. Functions as vectors.** Let $\mathcal{P}_2$ be the set of all polynomials of degree at most 2 in $x$ (i.e., $a_0 + a_1x + a_2x^2$ for real $a_0, a_1, a_2$), with ordinary function addition and scalar multiplication.

<ol type="a" markdown="1">
  <li>Verify $\mathcal{P}_2$ is closed under addition and scalar multiplication.</li>
  <li>Propose a basis for $\mathcal{P}_2$ using the Taylor-series basis functions from §7, and justify why it satisfies both basis requirements.</li>
  <li>Is $\mathcal{P}_2$ finite- or infinite-dimensional? Contrast this with the set of *all* polynomials (any degree).</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li>Adding two degree-$\le 2$ polynomials, $(a_0+a_1x+a_2x^2) + (b_0+b_1x+b_2x^2) = (a_0+b_0)+(a_1+b_1)x+(a_2+b_2)x^2$, is still degree $\le 2$; scaling by $\lambda$ similarly keeps every coefficient (and the degree bound) intact. Both operations stay inside $\mathcal{P}_2$.</li>
  <li>$\{1, x, x^2\}$ — the first three Taylor basis functions. They're <strong>linearly independent</strong> (no combination $c_0\cdot 1 + c_1 x + c_2 x^2$ is identically zero unless $c_0=c_1=c_2=0$), and every element of $\mathcal{P}_2$ is by definition exactly $a_0\cdot 1 + a_1\cdot x + a_2\cdot x^2$, so they <strong>span</strong> $\mathcal{P}_2$ as well.</li>
  <li><strong>Finite-dimensional</strong> — 3 basis functions, so $\mathcal{P}_2$ is a 3-dimensional vector space, even though its elements are functions. The set of all polynomials of any degree needs the full infinite basis $\{1, x, x^2, x^3, \dots\}$, making it infinite-dimensional — the same discrete-looking-but-secretly-continuous distinction as a truncated versus full Fourier series.</li>
</ol>

</details>

---

**Q6. Span and the zero vector.** A student claims: "$\operatorname{span}(\vec a)$ for a nonzero vector $\vec a$ is just the single point $\vec a$ itself, since that's the vector we started with."

<ol type="a" markdown="1">
  <li>Is the student correct? If not, describe $\operatorname{span}(\vec a)$ correctly.</li>
  <li>Does $\operatorname{span}(\vec a)$ contain the zero vector? Explain using the definition of span.</li>
  <li>If $\vec a$ itself is the zero vector, what is $\operatorname{span}(\vec a)$?</li>
</ol>

<details markdown="1">
<summary><strong>View Answer</strong></summary>

<ol type="a" markdown="1">
  <li><strong>No.</strong> $\operatorname{span}(\vec a) = \{\lambda \vec a : \lambda \in \mathbb{R}\}$ is every scalar multiple of $\vec a$ — an entire line through the origin in the direction of $\vec a$ — not just the single vector $\vec a$ (which is only the case $\lambda = 1$).</li>
  <li><strong>Yes.</strong> Choosing $\lambda = 0$ is always a valid scalar choice, and $0\cdot\vec a = \vec 0$, so the zero vector is always in the span of any set of vectors, nonzero or not.</li>
  <li>If $\vec a = \vec 0$, then $\lambda \vec a = \vec 0$ for every $\lambda$, so $\operatorname{span}(\vec a) = \{\vec 0\}$ — just the origin, a single point rather than a line.</li>
</ol>

</details>
