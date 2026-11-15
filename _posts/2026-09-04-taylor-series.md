---
layout: post
title: "Taylor Series: Why It Matters, and What Linear and Quadratic Approximation Really Mean"
date: 2026-09-04
tags:
  - Mathematics
related_posts: false
---

Almost every function you actually want to compute with — $\sin x$, $e^x$, $\sqrt{1+x}$, the solution to a nonlinear differential equation — is hard to evaluate, hard to integrate, or hard to reason about in closed form. Polynomials, by contrast, are the easiest functions there are: adding, multiplying, differentiating, and integrating them is pure algebra. A **Taylor series** is the precise statement of how to replace a complicated function, near a chosen point, with a polynomial that behaves just like it — matching not only its value but its slope, its curvature, and (if you keep going) every higher-order wiggle. This post builds the motivation first, then the formula, then makes exact what "linear approximation" and "quadratic approximation" mean as the first two terms of that same idea.

### 1. The problem: complicated functions, simple tools

Suppose you need $\sin(0.1)$. A calculator gives $0.099833\ldots$, but *how* does it get that number? Trigonometric, exponential, and logarithmic functions aren't built from finitely many additions and multiplications — there is no algebraic formula that produces $\sin x$ from $x$ in a fixed number of arithmetic steps. Every practical method for evaluating them — by hand, in a textbook table, or inside a CPU's floating-point unit — falls back on approximating the function by a polynomial that *is* built from finitely many additions and multiplications, and is accurate enough near the point of interest.

The same problem shows up one level up, in equations rather than single evaluations. Physical laws are almost always nonlinear — the pendulum equation $\ddot\theta + \frac{g}{L}\sin\theta = 0$, drag proportional to velocity squared, the exponential term in a chemical reaction rate — and nonlinear equations are, in general, not solvable in closed form. Replacing the nonlinear term with a polynomial approximation, valid near the operating point you care about, turns an intractable equation into a linear (or low-order polynomial) one that standard tools — linear algebra, Laplace transforms, eigenvalue analysis — can actually handle. That replacement is a Taylor series, truncated after one or two terms.

### 2. Where this actually gets used

The motivation above isn't abstract; the same trick recurs across essentially every quantitative field:

- **Numerical methods.** The central difference approximation for a second derivative, $\dfrac{d^2T}{dx^2}\Big\vert_{x_i} \approx \dfrac{T_{i+1} + T_{i-1} - 2T_i}{\Delta x^2}$, is derived by Taylor-expanding $T$ about $x_i$ in both directions and subtracting — the whole of finite-difference PDE solving rests on truncated Taylor expansions.
- **Linearization of nonlinear systems.** Control theory, structural dynamics, and circuit analysis routinely study a nonlinear system's behavior *near an equilibrium point* by keeping only the linear (first-order) term of a Taylor expansion — this is where "small-angle approximation," $\sin\theta \approx \theta$, comes from.
- **Root-finding and optimization.** Newton's method for solving $f(x) = 0$ walks along the tangent line — the linear Taylor approximation — of $f$ at the current guess; Newton's method for *minimizing* a function instead uses the quadratic approximation, because a parabola (unlike a line) actually has a minimum to jump to.
- **Error and uncertainty analysis.** If a measured quantity $x$ has a small error $\Delta x$, the resulting error in $f(x)$ is estimated as $f'(x)\,\Delta x$ — the linear term of a Taylor expansion — which is exactly the standard error-propagation formula used across engineering and the sciences.
- **Evaluating transcendental functions.** $e^x$, $\sin x$, $\cos x$, and $\ln(1+x)$ are computed in practice (in calculators, in software math libraries) via truncated Taylor (or closely related Chebyshev) polynomial approximations, because polynomials are all a processor can natively evaluate.

The throughline in every case: keep the first term (or first two, or first few) of a systematic polynomial approximation, and get something tractable in exchange for accuracy that is provably good *near* the expansion point — and provably degrades *away* from it.

### 3. The Taylor series equation

For a function $f$ that is smooth enough (has enough derivatives) at a point $x = a$, the **Taylor series** of $f$ about $a$ is

$$
f(x) = f(a) + f'(a)(x-a) + \frac{f''(a)}{2!}(x-a)^2 + \frac{f'''(a)}{3!}(x-a)^3 + \cdots = \sum_{n=0}^{\infty} \frac{f^{(n)}(a)}{n!}(x-a)^n .
$$

Read term by term, each one fixes up one more way the polynomial could fail to match $f$ at $a$: the zeroth term $f(a)$ matches the *value*; the first term $f'(a)(x-a)$ matches the *slope*; the second term $\frac{f''(a)}{2}(x-a)^2$ matches the *curvature*; and so on — the $n$th term is built exactly so that the polynomial's $n$th derivative at $a$ agrees with $f^{(n)}(a)$, while leaving every lower-order derivative undisturbed (because differentiating $(x-a)^n$ $k < n$ times still leaves a factor of $(x-a)$, which vanishes at $x=a$).

The special case $a = 0$ is common enough to have its own name, the **Maclaurin series**:

$$
f(x) = f(0) + f'(0)\,x + \frac{f''(0)}{2!}x^2 + \frac{f'''(0)}{3!}x^3 + \cdots .
$$

This is precisely the "powers of $x$ as a basis" idea: $\{1, x, x^2, x^3, \dots\}$ are the basis functions, and ${\frac{f^{(n)}(a)}{n!}}$ are the coordinates of $f$ in that basis, at least locally.

### 4. Linear approximation: keep the first two terms

Truncating the series after the linear term gives the **linear approximation** (also called the first-order Taylor approximation, or the tangent-line approximation):

$$
f(x) \approx L(x) = f(a) + f'(a)(x-a).
$$

Geometrically, $L(x)$ is the equation of the line tangent to $f$ at $x = a$ — same value, same slope, at that one point, and nothing more. It is a good approximation for $x$ close to $a$ and gets worse as $x$ moves away, because it has no way to represent the curving-away of $f$ from a straight line.

**Worked example.** Take $f(x) = \sin x$ and expand about $a = 0$ (so $f(0)=0$, $f'(x) = \cos x \Rightarrow f'(0) = 1$). The linear approximation is $L(x) = 0 + 1\cdot x = x$ — the familiar small-angle approximation $\sin x \approx x$. At $x = 0.1$: $L(0.1) = 0.1$, versus the true value $\sin(0.1) = 0.0998334\ldots$ — an error of about $1.67\times 10^{-4}$, i.e. good to roughly 3 significant figures with a single multiplication.

**Where linear approximation breaks down.** At $x = 1$ radian, $L(1) = 1$ versus the true $\sin(1) = 0.8415\ldots$ — an error of about $0.16$, nearly 19% off. The tangent line was only ever a *local* promise; nothing in its construction guarantees accuracy far from $a$, and $x=1$ is far enough from $0$ (about one radian of curvature) that the straight-line approximation has visibly separated from the curve.

### 5. Quadratic approximation: keep one more term

Adding back the second-order term gives the **quadratic approximation**:

$$
f(x) \approx Q(x) = f(a) + f'(a)(x-a) + \frac{f''(a)}{2}(x-a)^2.
$$

$Q(x)$ is the unique parabola through $(a, f(a))$ that matches both $f$'s slope *and* its curvature at $a$. Because it can bend the same way $f$ does, it tracks $f$ noticeably farther from $a$ than the tangent line does, at the cost of one extra term.

**Worked example (continuing $\sin x$ about $a=0$).** $f''(x) = -\sin x \Rightarrow f''(0) = 0$, so the quadratic term vanishes identically: $Q(x) = x$, no different from $L(x)$. This isn't a mistake — $\sin x$ is an *odd* function ($\sin(-x) = -\sin x$), so every even-order derivative at $0$ is zero and every even-order term drops out of its Maclaurin series entirely. To see the quadratic term do real work, expand $f(x) = \cos x$ about $a = 0$ instead: $f(0) = 1$, $f'(0) = -\sin 0 = 0$, $f''(0) = -\cos 0 = -1$, giving $Q(x) = 1 + 0\cdot x + \frac{-1}{2}x^2 = 1 - \frac{x^2}{2}$. At $x = 0.5$: $Q(0.5) = 1 - 0.125 = 0.875$, versus the true $\cos(0.5) = 0.87758\ldots$ — accurate to better than three decimal places, where the linear approximation $L(x) = 1$ (a flat, zero-slope line, since $f'(0)=0$) would have given a much cruder $1$, missing the curvature entirely.

**Counter-example: when there's no linear or quadratic approximation to write down.** Take $f(x) = \vert x\vert$ at $a = 0$. $f$ is continuous everywhere, but $f'(0)$ does not exist — the left- and right-hand slopes disagree ($-1$ versus $+1$), so there is no well-defined tangent line, and no Taylor series (linear, quadratic, or otherwise) can be built at $a=0$ at all. Smoothness — having the required derivative at the expansion point — is not optional; it's the standing assumption behind every term in Section 3.

### 6. The general pattern and the remainder

The same construction continues indefinitely: an $n$th-order Taylor approximation keeps terms through $(x-a)^n$ and matches $f$'s value and its first $n$ derivatives at $a$. What's left out — the true error of any truncated approximation — is captured by the **remainder term** $R_n(x) = f(x) - P_n(x)$, where $P_n$ is the degree-$n$ truncation. Taylor's theorem gives a precise bound on $R_n(x)$ in terms of the $(n+1)$th derivative, which is what justifies statements like "the linear approximation of $\sin x$ is accurate to within $\mathcal{O}(x^3)$" rather than leaving the approximation's quality as a guess.

**A subtlety worth flagging.** A function can have derivatives of every order at $a$ — so its full Taylor series exists as an infinite polynomial — and yet that series can fail to converge back to $f(x)$ anywhere except at $a$ itself. The standard example is $f(x) = e^{-1/x^2}$ (with $f(0):=0$), whose every derivative at $0$ is exactly zero, making its Maclaurin series the identically-zero function — while $f(x)$ itself is strictly positive for every $x \neq 0$. Existence of the series (the algebra of Section 3) and the series actually equaling the function (convergence) are two different claims; the second one is not automatic just because the first one holds.

### Recap

| Idea | What it says |
|---|---|
| Motivation | Complicated (transcendental, nonlinear) functions are hard to compute or solve with directly; polynomials are easy. Taylor series is the systematic way to trade one for the other, locally. |
| Taylor series | $f(x) = \sum_{n=0}^{\infty} \frac{f^{(n)}(a)}{n!}(x-a)^n$ — each term matches one more derivative of $f$ at $a$. |
| Maclaurin series | The special case $a = 0$. |
| Linear approximation | $f(x) \approx f(a) + f'(a)(x-a)$ — the tangent line; matches value and slope only. |
| Quadratic approximation | $f(x) \approx f(a) + f'(a)(x-a) + \frac{f''(a)}{2}(x-a)^2$ — the best-fit parabola; also matches curvature. |
| Remainder | $R_n(x) = f(x) - P_n(x)$, the truncation error; bounded via the $(n+1)$th derivative (Taylor's theorem). |
| Watch out for | Points where $f$ isn't differentiable (no series exists at all), and series that converge but not to $f$ itself. |
