---
layout: post
title: "Theory of Elasticity — Week 4: Einstein's Index Notation"
date: 2026-09-21
tags:
  - Theory of Elasticity
related_posts: false
---

Index notation compresses long component equations into one line. The motivating case is the momentum equation of a compressible viscous fluid, which is unreadable in components and compact in indices.

### 1. Motivation

$$
\rho\left(\frac{\partial u_i}{\partial t} + u_k\frac{\partial u_i}{\partial x_k}\right)
= -\frac{\partial p}{\partial x_i}
+ \frac{\partial}{\partial x_k}\left[\mu\left(\frac{\partial u_i}{\partial x_k} + \frac{\partial u_k}{\partial x_i} - \frac{2}{3}\delta_{ik}\frac{\partial u_l}{\partial x_l}\right)\right]
+ \frac{\partial}{\partial x_i}\left(\zeta\frac{\partial u_k}{\partial x_k}\right) + \rho g_i
$$

With the **comma convention** $\dfrac{\partial(\cdot)}{\partial x_i} = (\cdot)_{,i}$ and a dot for $\partial/\partial t$:

$$
\rho\left(\dot u_i + u_k u_{i,k}\right) = -p_{,i}
+ \mu_{,k}\left(u_{i,k} + u_{k,i} - \tfrac{2}{3}\delta_{ik}u_{l,l}\right)
+ \mu\left(u_{i,kk} + u_{k,ik} - \tfrac{2}{3}\delta_{ik}u_{l,lk}\right)
+ \zeta_{,i}u_{k,k} + \zeta u_{k,ki} + \rho g_i
$$

The last two terms are the product rule applied to $(\zeta u_{k,k})_{,i}$. One equation stands for three (free index $i$), each with sums over $k$ and $l$.

### 2. Summation convention

| Rule            | Meaning                                                                                      | Example                                                |
| --------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| **Dummy index** | An index repeated twice in one term is summed over $1,2,3$                                   | $a_{ii} = a_{11}+a_{22}+a_{33}$                        |
| **Free index**  | An index appearing once per term is not summed; it must appear in every term of the equation | $a_{ij}b_j = \sum_{j=1}^{3} a_{ij}b_j$, free index $i$ |
| **Renaming**    | A dummy index is a placeholder and can be renamed                                            | $a_i b_i = a_k b_k$                                    |
| **Rank**        | Number of free indices                                                                       | $p$: 0, $u_i$: 1, $\varepsilon_{ij}$: 2                |

**Matrix product.** $C_{ik} = a_{ij}b_{jk}$ has dummy $j$ and free $i,k$, so it is a matrix. For $n=3$:

$$
C_{i1} = a_{i1}b_{11} + a_{i2}b_{21} + a_{i3}b_{31}
$$

**Valid:** $c_i = a_{ij}b_j$ (free $i$ on both sides), $s = a_i b_i$ (no free index, scalar), $T_{ij} = a_i b_j$ (free $i,j$, outer product).

**Not valid:**

- $a_{ii}b_i$ has $i$ repeated three times; the convention allows at most two.
- $c_i = a_j b_j$ has free index $i$ on the left only, so the two sides have different rank.
- $c_i = a_{ij}b_{jj}$ has $j$ repeated three times.

Index equations must balance: every term has the same free indices.

### 3. Kronecker delta

$$
\delta_{ij} = \begin{cases} 1 & i = j \\ 0 & i \neq j \end{cases}
\qquad \delta_{12} = 0,\quad \delta_{22} = 1
$$

It is the identity matrix, and its properties follow from the definition:

| Identity                                                | Reason                                         |
| ------------------------------------------------------- | ---------------------------------------------- |
| $\delta_{kk} = \delta_{11}+\delta_{22}+\delta_{33} = 3$ | trace of the identity                          |
| $\delta_{ij}a_j = a_i$                                  | replaces the dummy index, hence "substitution" |
| $\delta_{ij}\delta_{jk} = \delta_{ik}$                  | identity times identity                        |
| $\delta_{ij}\delta_{ij} = \delta_{ii} = 3$              | sum of nine terms, three of them equal to 1    |

**Counter-examples:** $\delta_{ii} \neq 1$ (it is 3, not the single entry $\delta_{11}$), and $\delta_{ij}a_j \neq a_j$ (the free index of the result is $i$).

### 4. Permutation (Levi-Civita) symbol

$$
\epsilon_{ijk} = \begin{cases} +1 & (i,j,k) \text{ an even permutation of } (1,2,3) \\ -1 & \text{odd permutation} \\ 0 & \text{not a permutation (repeated index)} \end{cases}
$$

Even permutations are the cyclic orders (same sense as $1\to2\to3$); odd permutations are the reversed orders.

| Value | Triples                                                             |
| ----- | ------------------------------------------------------------------- |
| $+1$  | $\epsilon_{123},\ \epsilon_{231},\ \epsilon_{312}$                  |
| $-1$  | $\epsilon_{321},\ \epsilon_{213},\ \epsilon_{132}$                  |
| $0$   | $\epsilon_{233},\ \epsilon_{121}$, any triple with a repeated index |

Swapping any two indices flips the sign: $\epsilon_{ijk} = -\epsilon_{jik}$. Six of the 27 components are nonzero.

### 5. Cross product

$$
(\mathbf a \times \mathbf b)_i = \epsilon_{ijk}\,a_j b_k
$$

Check for $i=1$. Only $\epsilon_{123}=+1$ and $\epsilon_{132}=-1$ survive, because every other $\epsilon_{1jk}$ has a repeated index:

$$
(\mathbf a\times\mathbf b)_1 = \epsilon_{123}a_2b_3 + \epsilon_{132}a_3b_2 = a_2 b_3 - a_3 b_2
$$

Cyclic permutation gives the other two components, $a_3b_1 - a_1b_3$ and $a_1b_2 - a_2b_1$.

**Counter-example:** $a_2 b_1 - a_1 b_2 \neq a_1 b_2 - a_2 b_1$. The product is antisymmetric, so $\mathbf a\times\mathbf b = -\mathbf b\times\mathbf a$ and $\mathbf a\times\mathbf a = \mathbf 0$ (from $\epsilon_{ijk}a_ja_k = 0$, since $a_ja_k$ is symmetric in $j,k$ while $\epsilon_{ijk}$ is antisymmetric).

### 6. Differential operators in index form

With $p$ a scalar field and $\mathbf u$ a vector field:

| Operator            | Symbol                             | Index form                                     | Rank of result  |
| ------------------- | ---------------------------------- | ---------------------------------------------- | --------------- |
| Gradient of scalar  | $\nabla p$                         | $p_{,i}\,\mathbf e_i$                          | 1 (raises by 1) |
| Gradient of vector  | $\nabla\mathbf u$                  | $u_{i,j}\,\mathbf e_i\otimes\mathbf e_j$       | 2 (raises by 1) |
| Divergence          | $\nabla\cdot\mathbf u$             | $\operatorname{tr}(\nabla\mathbf u) = u_{i,i}$ | 0 (lowers by 1) |
| Curl                | $\nabla\times\mathbf u$            | $\epsilon_{ijk}\,u_{k,j}\,\mathbf e_i$         | 1 (keeps rank)  |
| Laplacian of scalar | $\nabla^2 p = \nabla\cdot\nabla p$ | $p_{,ii}$                                      | 0 (keeps rank)  |
| Laplacian of vector | $\nabla^2\mathbf u$                | $u_{i,kk}\,\mathbf e_i$                        | 1 (keeps rank)  |

The comma always opens a new index that belongs to the derivative: $u_{i,j}$ is the derivative of component $i$ with respect to $x_j$. In the momentum equation of section 1, $u_k u_{i,k} = (\mathbf u\cdot\nabla)u_i$ is the convective term.

**Counter-example:** $u_{i,j} \neq u_{j,i}$ in general. The gradient of a vector field is not symmetric, and its symmetric part defines the strain (section 7).

### 7. Strain from displacement

A **rigid-body motion** keeps all distances between points unchanged, so it produces no strain. The **strain** measures only the part of the displacement gradient that changes distances:

$$
\varepsilon_{ij} = \tfrac{1}{2}\left(u_{i,j} + u_{j,i}\right)
$$

**Example: simple shear of a rectangle** $L_x \times L_y$, with the top edge displaced by $\tfrac{1}{2}L_x$:

$$
\mathbf u = \begin{bmatrix} \tfrac{1}{2}\dfrac{L_x}{L_y}\,x_2 \\ 0 \\ 0 \end{bmatrix}
$$

{% include figure.liquid path="assets/img/elasticity/shear_strain.png" class="img-fluid rounded z-depth-1" caption="Simple shear: horizontal displacement grows linearly with height. Only $\varepsilon_{12}=\varepsilon_{21}$ is nonzero." %}

The only nonzero displacement gradient is $u_{1,2}$, so $u_{2,1} = 0$ and

$$
\varepsilon_{12} = \tfrac{1}{2}\left(u_{1,2} + u_{2,1}\right) = \tfrac{1}{2}u_{1,2} = \tfrac{1}{2}\cdot\tfrac{1}{2}\frac{L_x}{L_y} = \frac{1}{4}\frac{L_x}{L_y}
$$

All other components vanish, and $\varepsilon_{12}=\varepsilon_{21}$ by symmetry.

**Counter-example (rigid rotation).** A small rotation $\mathbf u = \boldsymbol\omega\times\mathbf x$ has $u_{1,2} = -\omega_3$ and $u_{2,1} = +\omega_3$, so

$$
\varepsilon_{12} = \tfrac{1}{2}\left(-\omega_3 + \omega_3\right) = 0
$$

The displacement gradient is nonzero, yet the strain is zero. This is why strain uses the symmetric part $\tfrac{1}{2}(u_{i,j}+u_{j,i})$ and not $u_{i,j}$ itself.

**Counter-example (uniform translation).** $\mathbf u = \mathbf c$ gives $u_{i,j}=0$ and hence zero strain; displacement and strain are different things.

### Recap

| Object                       | Index form                                   |
| ---------------------------- | -------------------------------------------- |
| Trace                        | $a_{ii}$                                     |
| Matrix–vector product        | $a_{ij}b_j$                                  |
| Matrix product               | $a_{ij}b_{jk}$                               |
| Identity matrix              | $\delta_{ij}$, with $\delta_{ii}=3$          |
| Cross product                | $\epsilon_{ijk}a_jb_k$                       |
| Gradient / divergence / curl | $p_{,i}$, $u_{i,i}$, $\epsilon_{ijk}u_{k,j}$ |
| Laplacian                    | $p_{,ii}$, $u_{i,kk}$                        |
| Strain                       | $\tfrac{1}{2}(u_{i,j}+u_{j,i})$              |

### Check Understanding

<details markdown="1">
<summary><strong>Q1.</strong> Is $c_i = a_{ij}b_j + d_k$ a valid index equation?</summary>

No. The first term has free index $i$ and the second has free index $k$, so the terms do not balance. A valid version is $c_i = a_{ij}b_j + d_i$.

</details>

<details markdown="1">
<summary><strong>Q2.</strong> Evaluate $\delta_{ij}\delta_{ij}$ and $\delta_{ij}a_ia_j$.</summary>

$\delta_{ij}\delta_{ij} = \delta_{jj} = 3$. And $\delta_{ij}a_ia_j = a_ja_j = |\mathbf a|^2$.

</details>

<details markdown="1">
<summary><strong>Q3.</strong> Find $\epsilon_{312}$, $\epsilon_{213}$, and $\epsilon_{113}$.</summary>

$\epsilon_{312} = +1$ (cyclic: $3\to1\to2$), $\epsilon_{213} = -1$ (one swap from $123$), and $\epsilon_{113} = 0$ (repeated index).

</details>

<details markdown="1">
<summary><strong>Q4.</strong> Use the permutation symbol to find $(\mathbf a\times\mathbf b)_3$ for $\mathbf a=(1,2,0)$, $\mathbf b=(0,1,3)$.</summary>

$(\mathbf a\times\mathbf b)_3 = \epsilon_{312}a_1b_2 + \epsilon_{321}a_2b_1 = a_1b_2 - a_2b_1 = 1\cdot1 - 2\cdot0 = 1$.

</details>

<details markdown="1">
<summary><strong>Q5.</strong> Write $\nabla\cdot(\nabla\times\mathbf u)$ in index form and show that it is zero.</summary>

$(\nabla\times\mathbf u)_i = \epsilon_{ijk}u_{k,j}$, so $\nabla\cdot(\nabla\times\mathbf u) = \epsilon_{ijk}u_{k,ji}$. The second derivative $u_{k,ji}$ is symmetric in $i,j$ (derivatives commute), while $\epsilon_{ijk}$ is antisymmetric in $i,j$, so the contraction vanishes.

</details>

<details markdown="1">
<summary><strong>Q6.</strong> For the displacement $\mathbf u = (\gamma x_2,\ -\gamma x_1,\ 0)$, compute $\varepsilon_{ij}$. Is the motion a deformation?</summary>

$u_{1,2} = \gamma$ and $u_{2,1} = -\gamma$, so $\varepsilon_{12} = \tfrac{1}{2}(\gamma - \gamma) = 0$ and all other components vanish. The strain is zero: this is a rigid rotation (about $x_3$, by angle $-\gamma$ for small $\gamma$), not a deformation.

</details>

<details markdown="1">
<summary><strong>Q7.</strong> Expand $\sigma_{ij} = \lambda\,\varepsilon_{kk}\,\delta_{ij} + 2G\,\varepsilon_{ij}$ for $i=j=1$ and for $i=1,\ j=2$.</summary>

$\sigma_{11} = \lambda(\varepsilon_{11}+\varepsilon_{22}+\varepsilon_{33}) + 2G\varepsilon_{11}$, and $\sigma_{12} = 2G\varepsilon_{12}$, since $\delta_{12}=0$ removes the first term.

</details>
