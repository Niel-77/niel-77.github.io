---
layout: post
title: "Modeling and Analysis of Mechanical Systems — Linear Operators and Matrix Decomposition"
date: 2026-10-20
tags:
  - Modeling and Analysis of Mechanical Systems
related_posts: false
---

A **linear operator** acts on a function the way a **matrix** acts on a vector. This post first checks when an operator is "symmetric" under the inner product, then covers the four standard matrix factorizations — LU, QR, eigenvalue decomposition (EVD) and singular value decomposition (SVD) — and when to use each.

### 1. Inner products and linear operators

For functions on $[a,b]$, the **inner product** is the dot product with the sum replaced by an integral:

$$
\langle f, g\rangle = \int_a^b f(x)\,g(x)\,dx.
$$

For a linear operator $L$ (e.g. $L = d^2/dx^2$), the operator is **self-adjoint** — the analogue of a symmetric matrix — when $\langle Lf, g\rangle = \langle f, Lg\rangle$. With $f(a)=f(b)=g(a)=g(b)=0$, integrating by parts twice gives

$$
\int_a^b f''g\,dx = \Big[g f'\Big]_a^b - \int_a^b g'f'\,dx = 0 - \Big[g' f\Big]_a^b + \int_a^b g''f\,dx = \int_a^b f\,g''\,dx.
$$

Both brackets vanish only because of the boundary conditions.

**Example 1 (satisfies).** Take $L=\dfrac{d^2}{dx^2}$ on $x\in[0,\pi]$ with

$$
f(x)=\sin x,\qquad g(x)=\sin 2x.
$$

Both vanish at $x=0$ and $x=\pi$, so the boundary conditions hold. Apply the operator:

$$
Lf=\frac{d^2}{dx^2}(\sin x)=-\sin x,\qquad Lg=\frac{d^2}{dx^2}(\sin 2x)=-4\sin 2x.
$$

Left side, using $\sin 2x=2\sin x\cos x$ and the substitution $u=\sin x$, $du=\cos x\,dx$ (so $u$ runs from $0$ to $0$):

$$
\langle Lf,g\rangle=\int_0^\pi(-\sin x)(\sin 2x)\,dx=-2\int_0^\pi\sin^2x\cos x\,dx=-2\int_0^{0}u^2\,du=0.
$$

Right side, by the same substitution:

$$
\langle f,Lg\rangle=\int_0^\pi\sin x\,(-4\sin 2x)\,dx=-8\int_0^\pi\sin^2x\cos x\,dx=-8\int_0^{0}u^2\,du=0.
$$

So $\langle Lf,g\rangle=\langle f,Lg\rangle=0$ ✓. Both are zero because $\sin x$ and $\sin 2x$ are orthogonal, which makes this a weak check.

**Example 2 (satisfies, nonzero).** Keep $f=\sin x$ on $[0,\pi]$ and take $g=x(\pi-x)$, which also vanishes at both ends. Then $Lf=-\sin x$ and, since $g'=\pi-2x$, $Lg=-2$.

$$
\langle Lf,g\rangle=-\int_0^\pi x(\pi-x)\sin x\,dx.
$$

Integrating by parts twice (the boundary terms vanish because $x(\pi-x)$ and $\sin x$ are both $0$ at the ends):

$$
\int_0^\pi x(\pi-x)\sin x\,dx=\int_0^\pi(\pi-2x)\cos x\,dx=2\int_0^\pi\sin x\,dx=4
\;\Rightarrow\;\langle Lf,g\rangle=-4.
$$

$$
\langle f,Lg\rangle=\int_0^\pi\sin x\,(-2)\,dx=-2\Big[-\cos x\Big]_0^\pi=-4\ \checkmark
$$

**Counter-example (boundary conditions fail).** On $[0,1]$, $f=x^2$, $g=1$: $\langle Lf,g\rangle=2$ but $\langle f,Lg\rangle=0$. The gap is the boundary term $[gf'-g'f]_0^1=2$.

**Counter-example (operator).** $L=d/dx$ is not self-adjoint even with zero boundary values: $\langle f',g\rangle=-\langle f,g'\rangle$ — a sign flip, like an anti-symmetric matrix.

### 2. Why decompose $A\vec x=\vec c$?

$A$ holds the physics (density, viscosity, stiffness, modulus, geometry, friction) and $\vec c$ the loads. We avoid forming $A^{-1}$ because factorizations are cheaper in **flops and memory**, and they expose structure: for large models or data, keep only the dominant modes. Keeping the largest eigenvalues (tied to energy content) retains 90–95 % or more of the information with far fewer modes — **Proper Orthogonal Decomposition (POD)** for simulations, **Principal Component Analysis (PCA)** for measured data (mathematically equivalent; the leading directions are the **dominant modes**).

| Method | Factorization         | Applies to                        | How the factors are found                                               |
| ------ | --------------------- | --------------------------------- | ----------------------------------------------------------------------- |
| LU     | $A = LU$              | Square                            | Gaussian elimination (multipliers); no eigenvalues                      |
| QR     | $A = QR$              | Any matrix, including rectangular | Gram–Schmidt, then $R=Q^TA$; no eigenvalues                             |
| EVD    | $A = Q\Lambda Q^{-1}$ | Square, diagonalizable            | **Eigenvalues, then eigenvectors of $A$** form $Q$                      |
| SVD    | $A = U\Sigma V^T$     | Any matrix, including rectangular | **Eigenvectors of $A^TA$** give $V$, then $\vec u_i=A\vec v_i/\sigma_i$ |

LU and QR are direct, finite procedures. EVD and SVD require solving an eigenproblem first, which for large matrices is done iteratively (the standard algorithm is built on repeated QR factorizations).

### 3. Positive definite matrices

For symmetric $A$, the quadratic form $\vec x^TA\vec x$ (the dot product of $\vec x$ and $A\vec x$) classifies the matrix. For $2\times2$, $\vec x^TA\vec x = a_{11}x_1^2 + (a_{12}+a_{21})x_1x_2 + a_{22}x_2^2$ — every term is degree two. If it is positive, the angle between $\vec x$ and $A\vec x$ is below 90°.

| Class                  | $\vec x^TA\vec x$ (all $\vec x\ne0$) | Eigenvalues         |
| ---------------------- | ------------------------------------ | ------------------- |
| Positive definite      | $> 0$                                | all $\lambda > 0$   |
| Positive semi-definite | $\ge 0$                              | all $\lambda \ge 0$ |
| Negative definite      | $< 0$                                | all $\lambda < 0$   |
| Negative semi-definite | $\le 0$                              | all $\lambda \le 0$ |
| Indefinite             | either sign                          | mixed signs         |

Also: if $U$ is invertible, $U^TU$ is positive definite, since $\vec x^TU^TU\vec x=\lVert U\vec x\rVert^2>0$.

**Examples of each class.**

$$
A_1=\begin{bmatrix}2&1\\1&2\end{bmatrix},\quad
A_2=\begin{bmatrix}1&1\\1&1\end{bmatrix},\quad
A_3=\begin{bmatrix}-2&0\\0&-3\end{bmatrix},\quad
A_4=\begin{bmatrix}-1&-1\\-1&-1\end{bmatrix},\quad
A_5=\begin{bmatrix}7&3\\3&-1\end{bmatrix}
$$

- **Positive definite — $A_1$:** $\lambda=1,3$, both positive. $\vec x^TA_1\vec x=x_1^2+x_2^2+(x_1+x_2)^2>0$ for every nonzero $\vec x$.
- **Positive semi-definite — $A_2$:** $\lambda=2,0$. $\vec x^TA_2\vec x=(x_1+x_2)^2\ge0$, and it is exactly $0$ at the nonzero vector $\vec x=[1,-1]^T$, which is why it is not positive definite.
- **Negative definite — $A_3$:** $\lambda=-2,-3$. $\vec x^TA_3\vec x=-2x_1^2-3x_2^2<0$ for every nonzero $\vec x$.
- **Negative semi-definite — $A_4$:** $\lambda=-2,0$. $\vec x^TA_4\vec x=-(x_1+x_2)^2\le0$, and it is $0$ at $\vec x=[1,-1]^T$.
- **Indefinite — $A_5$:** $\lambda=8,-2$. $\vec x=[1,0]^T$ gives $+7$ and $\vec x=[0,1]^T$ gives $-1$, so the sign depends on the vector.

**Pitfall: positive diagonal entries do not make a matrix positive definite.** The matrix with rows $[1,2]$ and $[2,1]$ has a positive diagonal but $\lambda=3,-1$, so it is indefinite: $\vec x=[1,1]^T$ gives $+6$ while $\vec x=[1,-1]^T$ gives $-2$. The off-diagonal terms matter.

### 4. LU decomposition

$$
A=LU,\qquad L \text{ lower triangular},\quad U \text{ upper triangular}.
$$

Then $LU\vec x=\vec c$ becomes two cheap solves with $\vec y=U\vec x$: $L\vec y=\vec c$ (forward substitution), then $U\vec x=\vec y$ (back substitution).

**Three versions** (a factorization has extra freedom that must be fixed):

1. **Doolittle:** $l_{ii}=1$.
2. **Crout:** $u_{ii}=1$.
3. **Cholesky:** symmetric positive definite $A$ only; $A=LL^T$ ($=U^TU$ with $U=L^T$), about half the work.

**Doolittle formulas.** Match entries of $A=LU$ with $l_{ii}=1$, row by row:

$$
\begin{aligned}
u_{1j}&=a_{1j}, \qquad l_{i1}=\frac{a_{i1}}{u_{11}},\\
u_{22}&=a_{22}-l_{21}u_{12}, \qquad u_{23}=a_{23}-l_{21}u_{13},\\
l_{32}&=\frac{a_{32}-l_{31}u_{12}}{u_{22}}, \qquad u_{33}=a_{33}-l_{31}u_{13}-l_{32}u_{23}.
\end{aligned}
$$

The $l_{ij}$ are the multipliers of Gaussian elimination, stored instead of discarded: $R_2\leftarrow R_2-l_{21}R_1$ zeroes $a_{21}$, and $U$ is what remains.

**Worked example.** $10x_1+x_2+x_3=15$, $x_1+10x_2+x_3=24$, $x_1+x_2+10x_3=33$:

$$
A=\begin{bmatrix}10&1&1\\1&10&1\\1&1&10\end{bmatrix}
=\underbrace{\begin{bmatrix}1&0&0\\\tfrac1{10}&1&0\\\tfrac1{10}&\tfrac1{11}&1\end{bmatrix}}_{L}
\underbrace{\begin{bmatrix}10&1&1\\0&\tfrac{99}{10}&\tfrac9{10}\\0&0&\tfrac{108}{11}\end{bmatrix}}_{U},
\qquad \vec c=\begin{bmatrix}15\\24\\33\end{bmatrix}.
$$

Here $l_{21}=l_{31}=\tfrac1{10}$, $u_{22}=10-\tfrac1{10}=\tfrac{99}{10}$, $u_{23}=1-\tfrac1{10}=\tfrac9{10}$, $l_{32}=\tfrac{1-\frac1{10}}{99/10}=\tfrac1{11}$ and $u_{33}=10-\tfrac1{10}-\tfrac1{11}\cdot\tfrac9{10}=\tfrac{108}{11}$.

Forward substitution, $L\vec y=\vec c$, then back substitution, $U\vec x=\vec y$:

$$
\vec y=\begin{bmatrix}15\\22.5\\\tfrac{324}{11}\end{bmatrix}
\;\Rightarrow\;
\vec x=\begin{bmatrix}1\\2\\3\end{bmatrix}.
$$

**Edge cases.**

- **Zero pivot.** The matrix with rows $[0,1]$, $[1,0]$ is invertible but $u_{11}=0$, so $l_{21}$ divides by zero. Swap rows first: $PA=LU$.
- **Cholesky needs positive definite.** For the indefinite matrix with rows $[1,2]$, $[2,1]$ from the §3 pitfall, $l_{11}=1$, $l_{21}=2$, $l_{22}^2=1-4<0$ — impossible (though that matrix has an ordinary $LU$).
- **Cholesky that works.** Rows $[4,2]$, $[2,3]$ (positive definite): $l_{11}=2$, $l_{21}=1$, $l_{22}=\sqrt2$.

### 5. QR decomposition

$$
A=QR,\qquad Q \text{ orthogonal (orthonormal columns)},\quad R \text{ upper triangular}.
$$

Orthonormal columns give $Q^TQ=I$, so $Q^{-1}=Q^T$ and $A\vec x=\vec c$ reduces to $R\vec x=Q^T\vec c$ (back substitution).

**Gram–Schmidt** builds $Q$ column by column from the columns $\vec a_i$ of $A$:

$$
\vec q_1=\frac{\vec a_1}{\lVert\vec a_1\rVert},\qquad
\vec q_2=\frac{\vec a_2-(\vec q_1\cdot\vec a_2)\,\vec q_1}{\lVert\vec a_2-(\vec q_1\cdot\vec a_2)\,\vec q_1\rVert},\qquad
\vec q_k=\frac{\vec a_k-\sum_{i<k}(\vec q_i\cdot\vec a_k)\,\vec q_i}{\lVert\cdots\rVert},
$$

and then $R=Q^TA$.

**Worked example.** $x_1+x_2=5$, $x_1-x_2=1$:

$$
A=\begin{bmatrix}1&1\\1&-1\end{bmatrix},\quad \vec c=\begin{bmatrix}5\\1\end{bmatrix},\quad
\vec a_1=\begin{bmatrix}1\\1\end{bmatrix},\quad \vec a_2=\begin{bmatrix}1\\-1\end{bmatrix}.
$$

$$
\vec q_1=\frac{\vec a_1}{\sqrt2}=\frac1{\sqrt2}\begin{bmatrix}1\\1\end{bmatrix},\qquad
\vec q_1\cdot\vec a_2=\tfrac1{\sqrt2}(1-1)=0
\;\Rightarrow\;
\vec q_2=\frac{\vec a_2}{\lVert\vec a_2\rVert}=\frac1{\sqrt2}\begin{bmatrix}1\\-1\end{bmatrix}.
$$

$$
Q=\frac1{\sqrt2}\begin{bmatrix}1&1\\1&-1\end{bmatrix},\qquad
R=Q^TA=\begin{bmatrix}\sqrt2&0\\0&\sqrt2\end{bmatrix},\qquad
Q^T\vec c=\frac1{\sqrt2}\begin{bmatrix}6\\4\end{bmatrix}
\;\Rightarrow\;
\vec x=\begin{bmatrix}3\\2\end{bmatrix}.
$$

**Why QR?** It handles **overdetermined** systems (more equations than unknowns — typical of fitting data in machine learning); it is numerically stable, since orthogonal $Q$ preserves lengths (and avoids the squared conditioning of $A^TA\vec x=A^T\vec c$); and repeated QR is the standard numerical route to eigenvalues.

**Edge cases.**

- Orthogonal columns are not enough — they must be _unit length_. The candidate with rows $[1,1]$, $[1,-1]$ has perpendicular columns of length $\sqrt2$, so it is not orthogonal until divided by $\sqrt2$. A rotation matrix is orthogonal.
- A $3\times2$ matrix (3 equations, 2 unknowns) has no LU, but does have a QR.
- For a square, well-conditioned system QR costs about twice LU, so LU remains cheaper there.

### 6. Eigenvalue decomposition (EVD)

$$
A=Q\Lambda Q^{-1},\qquad Q=[\vec v_1\ \cdots\ \vec v_n]\ (\text{eigenvectors}),\quad \Lambda=\operatorname{diag}(\lambda_1,\dots,\lambda_n),
$$

ordered $\lambda_1>\lambda_2>\dots>\lambda_n$.

**Why EVD?** (1) Natural frequencies $\omega$ and mode shapes; (2) stability; (3) POD / PCA, keeping the large-$\lambda$ modes; (4) cheap matrix powers, since the inner $Q^{-1}Q$ cancels:

$$
A^k=Q\Lambda^kQ^{-1}.
$$

**Symmetric case.** Eigenvectors of a symmetric matrix (distinct eigenvalues) are mutually orthogonal, so $Q$ is orthogonal, $Q^{-1}=Q^T$, and $A=Q\Lambda Q^T$ — no inverse needed. Stiffness and mass matrices are symmetric.

**Worked example.** Take $A_5$ from §3, with rows $[7,3]$ and $[3,-1]$.

$$
\det(A-\lambda I)=(7-\lambda)(-1-\lambda)-9=\lambda^2-6\lambda-16=(\lambda-8)(\lambda+2)=0
\;\Rightarrow\;\lambda_1=8,\ \lambda_2=-2.
$$

Solving $(A-\lambda I)\vec v=0$ for each (normalized):

$$
\lambda_1=8:\ \begin{bmatrix}-1&3\\3&-9\end{bmatrix}\vec v=0\Rightarrow\vec v_1=\frac1{\sqrt{10}}\begin{bmatrix}3\\1\end{bmatrix},
\qquad
\lambda_2=-2:\ \begin{bmatrix}9&3\\3&1\end{bmatrix}\vec v=0\Rightarrow\vec v_2=\frac1{\sqrt{10}}\begin{bmatrix}1\\-3\end{bmatrix}.
$$

$$
Q=\frac1{\sqrt{10}}\begin{bmatrix}3&1\\1&-3\end{bmatrix},\quad
\Lambda=\begin{bmatrix}8&0\\0&-2\end{bmatrix},\quad
Q\Lambda Q^T=\frac1{10}\begin{bmatrix}70&30\\30&-10\end{bmatrix}=A\ \checkmark
$$

**Discrete vs. continuous.** The operator $L=d^2/dx^2$ with zero boundary values on $[0,\pi]$ has $L(\sin nx)=-n^2\sin nx$: $\sin nx$ is an **eigenfunction** with eigenvalue $-n^2$, the same equation $A\vec v=\lambda\vec v$ with a function in place of a vector. As symmetric matrices have orthogonal eigenvectors, self-adjoint operators have orthogonal eigenfunctions — hence $\langle\sin x,\sin2x\rangle=0$ in §1. A matrix has finitely many eigenvectors; a continuous system has infinitely many eigenfunctions.

**Edge cases.**

- **Not diagonalizable.** The shear $B$ with rows $[1,2]$, $[0,1]$ has $\lambda=1,1$, but $(B-I)\vec v=0$ forces $v_2=0$: only the direction $[1,0]^T$. Two independent eigenvectors are needed, so $B$ has no EVD. Along that direction nothing rotates and, with $\lambda=1$, nothing stretches.
- **Rectangular matrices have no eigenvalues** ($A\vec v=\lambda\vec v$ needs a square $A$).
- **Eigenvectors are orthogonal only for symmetric matrices.** Eigenvalues can also be negative (above) or complex (a 90° rotation has $\lambda=\pm i$).

### 7. Singular value decomposition (SVD)

$$
A=U\Sigma V^T,\qquad A\vec v_i=\sigma_i\vec u_i,
$$

with $V$ the **right singular matrix**, $U$ the **left singular matrix** (both orthogonal) and $\Sigma$ the diagonal **singular value matrix**. It works for any matrix, including rectangular overdetermined systems ($m>n$).

|            | EVD                                           | SVD                       |
| ---------- | --------------------------------------------- | ------------------------- |
| Applies to | square matrices                               | any matrix                |
| Values     | $\lambda$ positive, negative, zero or complex | $\sigma\ge0$, always real |
| Vectors    | not necessarily orthogonal                    | always orthogonal         |
| Action     | stretch only                                  | rotate and stretch        |

The singular values are $\sigma_i=\sqrt{\lambda_i}$, where $\lambda_i$ are the eigenvalues of $A^TA$ (or $AA^T$). These matrices are symmetric and positive semi-definite, so $\lambda_i\ge0$ and the vectors are orthogonal.

**Why SVD?** Data storage (keep the largest $\sigma_i$), POD, and noise filtering (high-frequency noise sits in the smallest $\sigma_i$).

**Worked example.** Take the shear $B$ from §6, which has no EVD:

$$
A=\begin{bmatrix}1&2\\0&1\end{bmatrix}.
$$

$$
A^TA=\begin{bmatrix}1&2\\2&5\end{bmatrix},\qquad
\det(A^TA-\lambda I)=\lambda^2-6\lambda+1=0\;\Rightarrow\;\lambda=3\pm2\sqrt2
$$

$$
\sigma_1=\sqrt{5.828}=2.414,\qquad \sigma_2=\sqrt{0.172}=0.414.
$$

Right singular vectors (eigenvectors of $A^TA$, normalized):

$$
\vec v_1=\begin{bmatrix}0.383\\0.924\end{bmatrix}\ (67.5^\circ),\quad
\vec v_2=\begin{bmatrix}-0.924\\0.383\end{bmatrix},\quad
\vec v_1\cdot\vec v_2=0.
$$

Left singular vectors by the shortcut $\vec u_i=\tfrac1{\sigma_i}A\vec v_i$ (this also fixes the signs; the brute-force route, eigenvectors of $AA^T$, needs the signs matched by hand):

$$
\vec u_1=\frac1{2.414}\begin{bmatrix}2.231\\0.924\end{bmatrix}=\begin{bmatrix}0.924\\0.383\end{bmatrix},\qquad
\vec u_2=\frac1{0.414}\begin{bmatrix}-0.158\\0.383\end{bmatrix}=\begin{bmatrix}-0.383\\0.924\end{bmatrix}.
$$

$$
A=\underbrace{\begin{bmatrix}0.924&-0.383\\0.383&0.924\end{bmatrix}}_{U}
\underbrace{\begin{bmatrix}2.414&0\\0&0.414\end{bmatrix}}_{\Sigma}
\underbrace{\begin{bmatrix}0.383&0.924\\-0.924&0.383\end{bmatrix}}_{V^T}
$$

$A$ sends the perpendicular pair $\vec v_1,\vec v_2$ to the perpendicular pair $\sigma_1\vec u_1,\sigma_2\vec u_2$ — rotated and stretched by different amounts, which an eigen-direction cannot capture.

**Edge cases.**

- A computed $\sigma<0$ is always an error. A negative _eigenvalue_ is fine.
- For symmetric $A$, $\sigma_i=\lvert\lambda_i\rvert$. They equal the eigenvalues only if all $\lambda_i\ge0$. For $A_5$ in §3: $\lambda=8,-2$ but $\sigma=8,2$.
- For non-symmetric matrices there is no simple relation: the shear above has $\lambda=1,1$ but $\sigma=2.414,\ 0.414$.

### Recap

| Topic                 | Core idea                                                                                              |
| --------------------- | ------------------------------------------------------------------------------------------------------ |
| Self-adjoint operator | $\langle Lf,g\rangle=\langle f,Lg\rangle$; for $d^2/dx^2$ it needs vanishing boundary terms.           |
| Why decompose         | Avoid $A^{-1}$; save flops and memory; keep dominant modes (POD / PCA).                                |
| PD / PSD / ND         | $\vec x^TA\vec x>0\ /\ \ge0\ /\ <0 \iff$ all $\lambda>0\ /\ \ge0\ /\ <0$ (symmetric $A$).              |
| LU                    | $A=LU$; forward then back substitution. Doolittle ($l_{ii}=1$), Crout ($u_{ii}=1$), Cholesky ($LL^T$). |
| QR                    | $A=QR$, $R\vec x=Q^T\vec c$; Gram–Schmidt; any shape.                                                  |
| EVD                   | $A=Q\Lambda Q^{-1}$ ($Q\Lambda Q^T$ if symmetric); frequencies, powers; square and diagonalizable.     |
| SVD                   | $A=U\Sigma V^T$, $\sigma=\sqrt{\lambda(A^TA)}\ge0$; any matrix, orthogonal vectors.                    |

### Check our understanding

#### True or False

1. For $L=d^2/dx^2$ and functions vanishing at both ends of $[a,b]$, $\langle Lf,g\rangle=\langle f,Lg\rangle$.
2. $\langle f'',g\rangle=\langle f,g''\rangle$ holds for any twice-differentiable $f,g$, whatever their boundary values.
3. In Doolittle's method every diagonal entry of $L$ is 1.
4. In Crout's method every diagonal entry of $L$ is 1.
5. Cholesky decomposition applies to any invertible symmetric matrix.
6. Every invertible matrix has an LU decomposition without row swaps.
7. A symmetric matrix with $\vec x^TA\vec x>0$ for all nonzero $\vec x$ has only positive eigenvalues.
8. A symmetric matrix with all positive diagonal entries is positive definite.
9. A positive semi-definite matrix may have a zero eigenvalue.
10. A negative definite matrix has all eigenvalues negative.
11. The matrix with rows $[7,3]$ and $[3,-1]$ is positive definite.
12. If $U$ is invertible, $U^TU$ is positive definite.
13. If $Q$ is orthogonal, $Q^{-1}=Q^T$.
14. QR decomposition applies only to square matrices.
15. POD keeps the largest eigenvalues' modes, which can retain most of the energy with far fewer modes.
16. Eigenvectors of a symmetric matrix with distinct eigenvalues are mutually orthogonal.
17. If $A=Q\Lambda Q^{-1}$, then $A^k=Q\Lambda^kQ^{-1}$.
18. Eigenvalue decomposition applies directly to rectangular matrices.
19. Singular values are always real and non-negative.
20. The matrix with rows $[1,2]$ and $[0,1]$ has singular values equal to its eigenvalues (both 1).

<details markdown="1">
<summary><strong>View Answer Key</strong></summary>

1. **True.** Only boundary terms $[gf'-g'f]_a^b$ remain, and they vanish.
2. **False.** Counter-example: $f=x^2$, $g=1$ on $[0,1]$ gives $2$ vs. $0$.
3. **True.**
4. **False.** Crout puts the 1's on the diagonal of $U$.
5. **False.** It must also be positive definite; rows $[1,2]$, $[2,1]$ is symmetric and invertible but gives $l_{22}^2=-3$.
6. **False.** Rows $[0,1]$, $[1,0]$ have a zero first pivot and need a row swap.
7. **True.**
8. **False.** Rows $[1,2]$, $[2,1]$ have eigenvalues $3,-1$.
9. **True.** That is the difference between PSD ($\lambda\ge0$) and PD ($\lambda>0$).
10. **True.**
11. **False.** Its eigenvalues are $8$ and $-2$, so it is indefinite.
12. **True.** $\vec x^TU^TU\vec x=\lVert U\vec x\rVert^2>0$.
13. **True.**
14. **False.** QR exists for any matrix, including rectangular ones.
15. **True.**
16. **True.**
17. **True.** The inner $Q^{-1}Q$ factors cancel.
18. **False.** $A\vec v=\lambda\vec v$ needs a square matrix; use SVD.
19. **True.** $\sigma_i=\sqrt{\lambda_i(A^TA)}$ and $A^TA$ is positive semi-definite.
20. **False.** Eigenvalues are $1,1$; singular values are $2.414$ and $0.414$.

</details>

#### Practice questions

**Q1.** What is the condition on the eigenvalues when the matrix is (a) positive definite, (b) positive semi-definite, (c) negative definite?

<details markdown="1">
<summary><strong>View Answer</strong></summary>

(a) All $\lambda_i>0$. (b) All $\lambda_i\ge0$ — none negative, zero allowed. (c) All $\lambda_i<0$. Mixed signs mean the matrix is indefinite.

</details>

---

**Q2 (Problem 5-5).** Explain the analogy between a discrete system and a continuous function with respect to eigenvectors and eigenvalues.

<details markdown="1">
<summary><strong>View Answer</strong></summary>

| Discrete                      | Continuous                                       |
| ----------------------------- | ------------------------------------------------ |
| vector $\vec v$               | function $f(x)$                                  |
| matrix $A$                    | linear operator $L$ (e.g. $d^2/dx^2$)            |
| $A\vec v=\lambda\vec v$       | $Lf=\lambda f$                                   |
| eigenvector, eigenvalue       | eigenfunction, eigenvalue                        |
| dot product                   | inner product $\int fg\,dx$                      |
| symmetric matrix              | self-adjoint operator (with boundary conditions) |
| $n$ eigenvectors (finite DOF) | infinitely many eigenfunctions                   |

Symmetric matrices have orthogonal eigenvectors; self-adjoint operators have orthogonal eigenfunctions. Example: $L=d^2/dx^2$ on $[0,\pi]$ with zero boundary values has eigenfunctions $\sin nx$, eigenvalues $-n^2$. In both cases the eigenvalues give natural frequencies and the eigenvectors / eigenfunctions give the mode shapes.

</details>

---

**Q3 (Problem 5-5).** Where is QR decomposition better than LU decomposition? What limitations does it _not_ have compared to EVD?

<details markdown="1">
<summary><strong>View Answer</strong></summary>

**Better than LU:** it handles rectangular / overdetermined systems (LU needs a square matrix); it is more stable, because orthogonal $Q$ preserves lengths and needs no pivoting, and it avoids the squared conditioning of $A^TA$; and $Q^{-1}=Q^T$ is free. (LU is still about twice as cheap for a square, well-conditioned system.)

**Limitations of EVD that QR does not have:** EVD needs a square matrix; QR accepts any shape. EVD needs a diagonalizable matrix (the shear above fails); QR exists for every matrix. EVD's eigenvectors may be non-orthogonal and its eigenvalues complex; $Q$ in QR is always orthogonal and real.

**What QR does not give:** natural frequencies, mode shapes or dominant modes — that is what EVD and SVD provide.

</details>
