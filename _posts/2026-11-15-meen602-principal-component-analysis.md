---
layout: post
title: "Modeling and Analysis of Mechanical Systems — Principal Component Analysis (PCA)"
date: 2026-11-15
tags:
  - Modeling and Analysis of Mechanical Systems
related_posts: false
chart:
  plotly: true
---

Many engineering quantities depend on a long list of inputs. **Principal Component Analysis (PCA)** finds the few directions in that input space along which the data actually varies, so we can model with fewer variables. This post builds PCA from variance and covariance, walks through the full procedure on a CNC milling data set, and connects it to the SVD from the [matrix decomposition post]({% post_url 2026-10-20-meen602-matrix-decomposition %}).

### 1. Motivation: too many variables

Two examples of a quantity that depends on many inputs:

- **Critical heat flux** in boiling, $\text{CHF}=f(\text{Re},\,T_L,\,T_w,\,\theta,\,q,\dots)$ — Reynolds number, liquid and wall temperatures, orientation, heat input, and more.
- **Market value of a house**, $=f(\text{area},\,\text{zip code},\,\text{year built},\,\text{bedrooms},\dots)$.

Fitting a curve directly in all these variables tends to **overfit**: with many inputs and limited data, the model fits the noise instead of the trend. This is the **curse of dimensionality** — the amount of data needed to fill the input space grows exponentially with the number of dimensions.

Often the inputs are correlated, so the data really lives near a lower-dimensional subspace. Picture a 3-D cloud of points that is flat like a pancake: two directions carry nearly all the spread and the third is almost noise. PCA finds those directions:

- **PC1** — the direction of largest variance,
- **PC2** — the direction of largest remaining variance, orthogonal to PC1,
- and so on. Here 3 dimensions reduce to 2.

> **PCA** provides the mathematical tools to identify **orthogonal directions — the eigenvectors of the data covariance matrix — along which the variance of the data is maximized.** $\text{PC}_n$ is the $n^\text{th}$ principal component, the direction with the $n^\text{th}$ highest variance.

### 2. Variance

The **variance** is the average squared deviation of the data from their sample mean. For $m$ observations $x_1,\dots,x_m$ with mean $\bar x$,

$$
\operatorname{Var}(X)=\frac{\sum_{i=1}^{m}(x_i-\bar x)(x_i-\bar x)}{m-1}\ \ge 0 .
$$

We divide by $m-1$, the **degrees of freedom**, not $m$ (Bessel's correction). One degree of freedom is used up by estimating $\bar x$ from the same data; dividing by $m$ would underestimate the true variance on average.

### 3. Covariance

For two variables $X$ and $Y$,

$$
\operatorname{Cov}(X,Y)=\frac{\sum_{i=1}^{m}(x_i-\bar x)(y_i-\bar y)}{m-1}.
$$

**Computational (sum-of-products) form.** Expanding the product and using $\sum x_i=m\bar x$, $\sum y_i=m\bar y$ gives an equivalent formula that needs only running sums of $x$, $y$ and $xy$, with no deviations computed first:

$$
\operatorname{Cov}(X,Y)=\frac{\sum_{i=1}^{m}x_iy_i-\dfrac{1}{m}\Big(\sum_{i=1}^{m}x_i\Big)\Big(\sum_{i=1}^{m}y_i\Big)}{m-1}
=\frac{\sum_{i=1}^{m}x_iy_i-m\,\bar x\,\bar y}{m-1}.
$$

Setting $Y=X$ gives the matching variance formula, $\operatorname{Var}(X)=\big[\sum x_i^2-\tfrac1m(\sum x_i)^2\big]/(m-1)$.

_Derivation:_ $\sum(x_i-\bar x)(y_i-\bar y)=\sum x_iy_i-\bar y\sum x_i-\bar x\sum y_i+m\bar x\bar y=\sum x_iy_i-m\bar x\bar y$.

_Check with the CNC data of §7_ (cutting speed $V$, vibration amplitude $A$, $m=10$): $\sum V=1740$, $\sum A=266.5$, $\sum VA=50355$, so

$$
\operatorname{Cov}(V,A)=\frac{50355-\tfrac{1740\times266.5}{10}}{9}=\frac{50355-46371}{9}=442.67,
$$

the same as the deviation form. _Caution:_ on a computer this form subtracts two large, nearly equal numbers, so it can lose precision badly when the mean is large relative to the spread. Use it for hand calculation; numerical libraries use the deviation form (or a one-pass update such as Welford's algorithm).

Unlike the variance, it can take any sign:

| Sign                   | Meaning                                                                                                           |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------- |
| $\operatorname{Cov}>0$ | The variables move in the same direction: $X\uparrow\Rightarrow Y\uparrow$, $X\downarrow\Rightarrow Y\downarrow$. |
| $\operatorname{Cov}<0$ | Inverse relationship: $X\uparrow\Rightarrow Y\downarrow$.                                                         |
| $\operatorname{Cov}=0$ | $X$ and $Y$ are **uncorrelated** (no _linear_ relationship).                                                      |

**Uncorrelated is not independent.** With $x=[-1,0,1]$ and $y=x^2=[1,0,1]$, $\operatorname{Cov}(X,Y)=0$, yet $Y$ is completely determined by $X$. Covariance only detects linear trends.

**The magnitude of covariance is not meaningful on its own.** It does not say whether two data sets are closely or loosely correlated, because it scales with the units. Measuring $X$ in centimetres instead of metres multiplies the covariance by 100 without changing the relationship at all. The fix is to normalize by the standard deviations, which gives the **correlation coefficient**

$$
\rho_{XY}=\frac{\operatorname{Cov}(X,Y)}{\sqrt{\operatorname{Var}(X)\operatorname{Var}(Y)}}\in[-1,1].
$$

### 4. The covariance matrix

Stack the $m$ observations of $n$ variables as rows of a data matrix $X\in\mathbb R^{m\times n}$ and subtract each column's mean to get the centered matrix $X_c$. Then all variances and covariances sit in one $n\times n$ matrix:

$$
C=\frac{1}{m-1}X_c^TX_c,\qquad C_{jj}=\operatorname{Var}(X_j),\quad C_{jk}=\operatorname{Cov}(X_j,X_k).
$$

$C$ is symmetric and positive semi-definite (it has the form $U^TU$). From the decomposition post this guarantees real, non-negative eigenvalues and orthogonal eigenvectors:

$$
C=Q\Lambda Q^T,\qquad \lambda_1\ge\lambda_2\ge\dots\ge\lambda_n\ge0 .
$$

- The eigenvectors $\vec q_j$ are the **principal directions** (PC1, PC2, …).
- The eigenvalue $\lambda_j$ is the **variance of the data along** $\vec q_j$.
- The **explained variance ratio** of the first $k$ components is $\sum_{j\le k}\lambda_j\big/\sum_j\lambda_j$ — the same idea as the energy fraction in SVD truncation.
- The **scores** (the data in the new coordinates) are $Z=X_cQ$. Keeping the first $k$ columns gives the reduced data.

Why the top eigenvector maximizes variance: the variance of the data projected on a unit vector $\vec w$ is $\vec w^TC\vec w$, and the maximum of this quadratic form over $\lVert\vec w\rVert=1$ is $\lambda_1$, reached at $\vec w=\vec q_1$.

### 5. PCA through SVD

Take the SVD of the centered data instead, $X_c=U\Sigma V^T$. Then

$$
C=\frac{1}{m-1}X_c^TX_c=\frac{1}{m-1}V\Sigma^2V^T
\quad\Longrightarrow\quad
Q=V,\qquad \lambda_j=\frac{\sigma_j^2}{m-1},\qquad Z=X_cV=U\Sigma .
$$

So PCA is the SVD of the centered data. In practice this is the preferred route: it never forms $X_c^TX_c$, which would square the condition number. Keeping $k$ components is exactly the rank-$k$ truncation of the previous post, and the explained variance ratio is the energy fraction $\sum_{i\le k}\sigma_i^2/\sum\sigma_i^2$.

### 6. Worked example

Four observations of two variables:

$$
x=[2,\,4,\,6,\,8],\qquad y=[1,\,3,\,2,\,5],\qquad \bar x=5,\quad \bar y=2.75 .
$$

Deviations: $x-\bar x=[-3,-1,1,3]$, $y-\bar y=[-1.75,\,0.25,\,-0.75,\,2.25]$, with $m-1=3$.

$$
\operatorname{Var}(X)=\frac{9+1+1+9}{3}=6.667,\qquad
\operatorname{Var}(Y)=\frac{3.0625+0.0625+0.5625+5.0625}{3}=2.917,
$$

$$
\operatorname{Cov}(X,Y)=\frac{5.25-0.25-0.75+6.75}{3}=3.667,\qquad
\rho=\frac{3.667}{\sqrt{6.667\times2.917}}=0.83 .
$$

$$
C=\begin{bmatrix}6.667&3.667\\3.667&2.917\end{bmatrix},\qquad
\det(C-\lambda I)=\lambda^2-9.583\lambda+6=0
\;\Rightarrow\;\lambda_1=8.910,\ \lambda_2=0.673 .
$$

$$
\vec q_1=\begin{bmatrix}0.853\\0.522\end{bmatrix},\qquad
\vec q_2=\begin{bmatrix}-0.522\\0.853\end{bmatrix},\qquad
\frac{\lambda_1}{\lambda_1+\lambda_2}=93.0\%.
$$

PC1 alone explains 93 % of the variance, so each 2-D point can be replaced by a single score $z_1=\vec q_1\cdot(\vec x_i-\bar{\vec x})$:

$$
z_1=[\,3.47,\ 0.72,\ -0.46,\ -3.73\,]\quad(\text{up to an overall sign}).
$$

Check by SVD: the singular values of $X_c$ satisfy $\sigma_j^2/3=8.910,\ 0.673$ — the same eigenvalues.

**Scaling changes the answer.** If $x$ is recorded in centimetres ($100x$), $\operatorname{Cov}$ becomes $366.7$ while $\rho$ stays $0.83$, and PC1 swings to point almost entirely along $x$ simply because its numbers are bigger. When variables have different units (temperatures, Reynolds numbers, areas), **standardize each column** (subtract the mean, divide by the standard deviation) first. This is PCA on the correlation matrix.

### 7. PCA step by step: CNC milling example

**Objectives of PCA.** (1) Identify the directions (vectors) along which the data has the highest variation. (2) Compress the data for efficient storage and processing without losing its essential features.

**Data.** Ten CNC milling runs, each recording $n=3$ features:

| Obs.       | $x_1$: cutting speed $V$ (m/min) | $x_2$: feed rate $f$ (mm/rev) | $x_3$: vibration amplitude $A$ (µm) |
| ---------- | -------------------------------- | ----------------------------- | ----------------------------------- |
| 1          | 120                              | 0.05                          | 12.4                                |
| 2          | 120                              | 0.08                          | 14.1                                |
| 3          | 150                              | 0.05                          | 15.9                                |
| 4          | 150                              | 0.08                          | 18.2                                |
| 5          | 180                              | 0.05                          | 22.1                                |
| 6          | 180                              | 0.08                          | 25.4                                |
| 7          | 200                              | 0.10                          | 31.7                                |
| 8          | 200                              | 0.12                          | 35.2                                |
| 9          | 220                              | 0.10                          | 42.6                                |
| 10         | 220                              | 0.12                          | 48.9                                |
| Mean       | 174                              | 0.083                         | 26.65                               |
| Sample var | 1404.44                          | $7.34\times10^{-4}$           | 157.54                              |

Sample covariances: $\operatorname{Cov}(V,f)=0.72$, $\operatorname{Cov}(f,A)=0.282$, $\operatorname{Cov}(V,A)=442.67$.

#### Step 1 — Organize the data matrix

Put the observations in the rows and the features in the columns: $X\in\mathbb R^{m\times n}$, here $m=10$ observations and $n=3$ features, so $X$ is $10\times3$.

#### Step 2 — Check the orders of magnitude; standardize if needed

The variances differ by six orders of magnitude ($1404$ vs. $7\times10^{-4}$). Unscaled, the covariance matrix would be dominated by cutting speed simply because its numbers are large: PCA on the raw data puts 99 % of the variance on a PC1 that is essentially $V$ alone, which says nothing about the physics. So convert each column to **z-scores**:

$$
z_{ij}=\frac{x_{ij}-\bar x_j}{s_j},\qquad s_j=\sqrt{\frac{\sum_{i=1}^{m}(x_{ij}-\bar x_j)^2}{m-1}},
$$

with $s_V=37.48$, $s_f=0.0271$, $s_A=12.55$. Every column of $Z\in\mathbb R^{m\times n}$ now has mean 0 and variance 1:

$$
Z=\begin{bmatrix}
-1.441&-1.218&-1.135\\
-1.441&-0.111&-1.000\\
-0.640&-1.218&-0.857\\
-0.640&-0.111&-0.673\\
\phantom{-}0.160&-1.218&-0.363\\
\phantom{-}0.160&-0.111&-0.100\\
\phantom{-}0.694&\phantom{-}0.627&\phantom{-}0.402\\
\phantom{-}0.694&\phantom{-}1.365&\phantom{-}0.681\\
\phantom{-}1.228&\phantom{-}0.627&\phantom{-}1.271\\
\phantom{-}1.228&\phantom{-}1.365&\phantom{-}1.773
\end{bmatrix}.
$$

Check one entry: $z_{11}=(120-174)/37.48=-1.441$.

#### Step 3 — Construct the covariance matrix

$$
C=\frac{1}{m-1}Z^TZ=
\begin{bmatrix}
\operatorname{Var}(z_1)&\operatorname{Cov}(z_1,z_2)&\operatorname{Cov}(z_1,z_3)\\
\operatorname{Cov}(z_2,z_1)&\operatorname{Var}(z_2)&\operatorname{Cov}(z_2,z_3)\\
\operatorname{Cov}(z_3,z_1)&\operatorname{Cov}(z_3,z_2)&\operatorname{Var}(z_3)
\end{bmatrix}
=\begin{bmatrix}1&0.709&0.941\\0.709&1&0.829\\0.941&0.829&1\end{bmatrix}.
$$

Because $Z$ is standardized, the diagonal is all ones and the off-diagonal entries are the **correlation coefficients** of the original variables, e.g. $\rho_{VA}=442.67/(37.48\times12.55)=0.941$. $C$ is used because:

1. It quantifies the linear relationships and dependencies among the features.
2. It is square and symmetric, $\operatorname{Cov}(x_i,x_j)=\operatorname{Cov}(x_j,x_i)$, so its EVD exists with real eigenvalues and orthogonal eigenvectors. The eigenvectors are the principal components, the directions along which the variance is maximum.

Here every off-diagonal entry is positive, so all three variables are positively correlated: faster speeds and higher feeds go with larger vibration. (Nonzero entries alone only say the variables are correlated; it is the positive _sign_ that says they move in the same direction.)

#### Step 4 — Eigenvalue decomposition: eigenvalues and loading matrix

Solve $\det(C-\lambda I)=\lambda^3-3\lambda^2+0.924\lambda-0.031=0$, and sort the eigenvalues in decreasing order:

$$
\lambda_1=2.656,\qquad \lambda_2=0.306,\qquad \lambda_3=0.038,\qquad \sum\lambda_j=3=\operatorname{tr}(C)=n .
$$

The sum equals $n$ because each standardized variable contributes variance 1. Place the unit eigenvectors as columns, left to right in the same order, to get the **loading matrix** (also called the weight or rotation matrix) $W\in\mathbb R^{n\times n}$:

$$
W=\begin{bmatrix}\vec w_1&\vec w_2&\vec w_3\end{bmatrix}
=\begin{bmatrix}
0.578&\phantom{-}0.571&\phantom{-}0.583\\
0.549&-0.800&\phantom{-}0.240\\
0.603&\phantom{-}0.182&-0.777
\end{bmatrix},
\qquad \vec w_1\leftrightarrow\lambda_1,\ \vec w_2\leftrightarrow\lambda_2,\ \vec w_3\leftrightarrow\lambda_3 .
$$

| PC  | $\lambda_j$ | Explained | Cumulative | Reading the loadings                                          |
| --- | ----------- | --------- | ---------- | ------------------------------------------------------------- |
| PC1 | 2.656       | 88.5 %    | 88.5 %     | All three weights $\approx0.58$: overall "cutting severity".  |
| PC2 | 0.306       | 10.2 %    | 98.7 %     | Feed ($-0.80$) against speed ($+0.57$): the feed–speed trade. |
| PC3 | 0.038       | 1.3 %     | 100 %      | Mostly residual noise.                                        |

The sign of each eigenvector is arbitrary ($-\vec w_j$ is equally valid); here the first entry is taken positive.

The scree plot shows how quickly the variance is captured. The 3-D plot shows the standardized runs and the three principal axes (drag to rotate): the points lie close to a plane spanned by PC1 and PC2, and PC1 runs along the diagonal of the cloud from run 1 (slow, light feed) to run 10 (fast, heavy feed). Rotate until you look along PC2 (orange line shrinks to a point): the cloud is then almost a straight line along PC1, and looking along PC1 shows how thin it is in the PC3 direction — that thinness is why $k=2$ loses so little.

```plotly
{"data":[{"type":"bar","x":["PC1","PC2","PC3"],"y":[88.5,10.2,1.3],"name":"explained (%)","marker":{"color":"steelblue"},"text":["88.5%","10.2%","1.3%"],"textposition":"auto"},{"type":"scatter","x":["PC1","PC2","PC3"],"y":[88.5,98.7,100.0],"mode":"lines+markers","name":"cumulative (%)","line":{"color":"crimson"}}],"layout":{"title":{"text":"Explained variance (scree plot)"},"yaxis":{"title":{"text":"% of total variance"},"range":[0,105]},"height":400,"legend":{"orientation":"h","y":-0.2}}}
```

```plotly
{"data":[{"type":"scatter3d","x":[-1.441,-1.441,-0.64,-0.64,0.16,0.16,0.694,0.694,1.227,1.227],"y":[-1.218,-0.111,-1.218,-0.111,-1.218,-0.111,0.627,1.365,0.627,1.365],"z":[-1.135,-1.0,-0.856,-0.673,-0.363,-0.1,0.402,0.681,1.271,1.773],"mode":"markers+text","text":["1","2","3","4","5","6","7","8","9","10"],"marker":{"size":5,"color":"black"},"name":"runs (z-scores)"},{"type":"scatter3d","x":[-1.884,1.884],"y":[-1.791,1.791],"z":[-1.967,1.967],"mode":"lines","line":{"color":"crimson","width":8},"name":"PC1 (88.5%)"},{"type":"scatter3d","x":[-0.632,0.632],"y":[0.885,-0.885],"z":[-0.201,0.201],"mode":"lines","line":{"color":"darkorange","width":8},"name":"PC2 (10.2%)"},{"type":"scatter3d","x":[-0.226,0.226],"y":[-0.093,0.093],"z":[0.302,-0.302],"mode":"lines","line":{"color":"royalblue","width":8},"name":"PC3 (1.3%)"}],"layout":{"title":{"text":"CNC runs in standardized space; PC axes drawn \u00b12 standard deviations"},"scene":{"xaxis":{"title":{"text":"z\u2081 speed"},"range":[-3.4,3.4]},"yaxis":{"title":{"text":"z\u2082 feed"},"range":[-3.4,3.4]},"zaxis":{"title":{"text":"z\u2083 vibration"},"range":[-3.4,3.4]},"aspectmode":"cube","camera":{"eye":{"x":-0.75,"y":-2.0,"z":0.5}}},"height":620,"legend":{"orientation":"h"},"margin":{"l":0,"r":0,"t":50,"b":0}}}
```

#### Step 5 — Project onto the principal components (scores)

To compress, keep only the first $k<n$ columns, $W_k\in\mathbb R^{n\times k}$, and project:

$$
S_k=Z\,W_k,\qquad (m\times n)(n\times k)=(m\times k).
$$

If $k=n$ there is no compression. With $k=2$ (98.7 % of the variance), $W_2$ is $3\times2$ and

$$
S_2=Z\,W_2=
\begin{bmatrix}
-2.187&-0.055\\
-1.497&-0.916\\
-1.556&\phantom{-}0.453\\
-0.837&-0.400\\
-0.795&\phantom{-}1.000\\
-0.028&\phantom{-}0.162\\
\phantom{-}0.988&-0.033\\
\phantom{-}1.562&-0.573\\
\phantom{-}1.821&\phantom{-}0.430\\
\phantom{-}2.529&-0.070
\end{bmatrix}\quad(10\times2).
$$

**What is $S$?** It has the same number of rows (observations) as the data, but its columns are the principal components, not physical quantities like speed, force or temperature. Each entry is an observation's coordinate along a PC. The variance of column $j$ is exactly $\lambda_j$ (here $2.656$ and $0.306$), and the columns are uncorrelated with each other. Run 1 (slow, light feed) has the most negative PC1 score and run 10 (fast, heavy feed) the most positive, as PC1 = "severity" predicts. Because the eigenvectors of a symmetric matrix are orthonormal, the full $W$ is an orthogonal (rotation) matrix: $W^TW=I$ and $W^{-1}=W^T$.

#### Step 6 — Reconstruct the data

**Full reconstruction** ($k=n$). Since $S=ZW$ and $W^{-1}=W^T$,

$$
SW^T=ZWW^T=Z \qquad\text{(exact)}.
$$

**Truncated reconstruction** ($k<n$). $W_k$ has orthonormal columns, so $W_k^TW_k=I_k$, but $W_kW_k^T\neq I_n$: it is the projection onto the kept PCs. So we only get an approximation:

$$
Z_\text{recon}=S_kW_k^T\approx Z,\qquad
X_\text{recon}=Z_\text{recon}\operatorname{diag}(s_1,\dots,s_n)+\mathbf 1\,\bar{\vec x}^T .
$$

The last step undoes the standardization, multiplying each column by $s_j$ and adding back $\bar x_j$, to return to physical units.

For $k=2$, the first and last runs come back as

$$
\text{run 1: }(120,\ 0.050,\ 12.4)\ \to\ (125.5,\ 0.052,\ 10.0),\qquad
\text{run 10: }(220,\ 0.120,\ 48.9)\ \to\ (227.3,\ 0.122,\ 45.6).
$$

The relative error is $\lVert Z-Z_\text{recon}\rVert_F/\lVert Z\rVert_F=\sqrt{\lambda_3/\sum\lambda_j}=\sqrt{0.0126}=11.2\%$ in standardized units (2.4 % of $\lVert X\rVert_F$ in physical units). As in the SVD post, the variance kept (98.7 %) is a squared measure, so it overstates the accuracy. Storage drops from $10\times3=30$ numbers to $10\times2+3\times2=26$, plus the means and standard deviations. The saving is small for such a small table, but it grows with $m$: for $10^6$ runs it is about one third.

#### Recap: the whole pipeline and its dimensions

Each step is a matrix operation, and checking the sizes is the quickest way to catch a mistake (e.g. multiplying by $W_k$ instead of $W_k^T$).

| Matrix                          | Operation                                                            | General     | CNC example                              |
| ------------------------------- | -------------------------------------------------------------------- | ----------- | ---------------------------------------- |
| Data $X$                        | Assemble: rows = observations, columns = features                    | $m\times n$ | $10\times3$                              |
| z-score matrix $Z$              | Standardize each column: $z_{ij}=(x_{ij}-\bar x_j)/s_j$              | $m\times n$ | $10\times3$                              |
| Covariance matrix $C$           | $C=\frac{1}{m-1}Z^TZ$                                                | $n\times n$ | $3\times3$                               |
| Loading matrix $W$              | EVD of $C$; eigenvectors as columns, $\lambda_1\ge\lambda_2\ge\dots$ | $n\times n$ | $3\times3$                               |
| Truncated loading $W_k$         | Keep the first $k$ columns (PC1, …, PC$k$)                           | $n\times k$ | $3\times2$ for $k=2$                     |
| Score matrix $S_k$              | $S_k=ZW_k$: $(m\times n)(n\times k)$                                 | $m\times k$ | $10\times3$ ($k=3$), $10\times2$ ($k=2$) |
| Reconstruction $Z_\text{recon}$ | $Z_\text{recon}=S_kW_k^T$: $(m\times k)(k\times n)$                  | $m\times n$ | $10\times3$                              |
| Reconstruction $X_\text{recon}$ | Undo the standardization: $x_{ij}=s_jz_{ij}+\bar x_j$                | $m\times n$ | $10\times3$                              |

Compression happens only in the middle: $S_k$ has $k<n$ columns. With $k=n$ ($W$ full), $Z_\text{recon}=Z$ exactly; with $k<n$, $Z_\text{recon}$ keeps the full size $m\times n$ but is only an approximation, because $W_kW_k^T\neq I$.

**Link to §5.** The same result comes from the SVD of the standardized data, $Z=U\Sigma V^T$: $W=V$, $\lambda_j=\sigma_j^2/(m-1)$ and $S=U\Sigma$, without ever forming $C$.

### 8. Limitations

- **Linear only.** PCA finds flat subspaces. Data on a curved surface needs kernel PCA, autoencoders or other manifold-learning methods.
- **Variance is not relevance.** The highest-variance direction need not be the one that predicts the output (e.g. CHF). PCA never looks at the output. If prediction is the goal, partial least squares (PLS) or active subspaces use the output too.
- **Sensitive to outliers and scaling**, since it is built from squared deviations.
- **Components are mixtures** of the original variables, which makes them harder to interpret physically than a selected subset of inputs.

### Recap

| Topic             | Core idea                                                                                                                                        |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Why PCA           | Many correlated inputs → overfitting; find the few directions that carry the variance.                                                           |
| Variance          | $\sum(x_i-\bar x)^2/(m-1)\ge0$; $m-1$ = degrees of freedom.                                                                                      |
| Covariance        | Sign gives direction of the linear relationship; magnitude depends on units → use $\rho$.                                                        |
| Covariance matrix | $C=X_c^TX_c/(m-1)$, symmetric PSD; eigenvectors = PCs, eigenvalues = variances along them.                                                       |
| PCA via SVD       | $X_c=U\Sigma V^T$: PCs $=V$, $\lambda_j=\sigma_j^2/(m-1)$, scores $=U\Sigma$.                                                                    |
| Choosing $k$      | Explained variance $\sum_{j\le k}\lambda_j/\sum\lambda_j$, typically $\ge90\%$.                                                                  |
| PCA steps         | Organize $X$ → standardize to $Z$ → $C=Z^TZ/(m-1)$ → EVD, sort $\lambda$, form $W$ → scores $S_k=ZW_k$ → reconstruct $S_kW_k^T$, un-standardize. |

### Check our understanding

#### True or False

1. Variance can be negative.
2. Covariance can be negative.
3. $\operatorname{Cov}(X,Y)=0$ means $X$ and $Y$ are independent.
4. A larger covariance always means a stronger relationship.
5. The covariance matrix is symmetric and positive semi-definite.
6. Principal components are mutually orthogonal.
7. The eigenvalue of a principal component equals the variance of the data along it.
8. The principal directions are the right singular vectors of the centered data matrix.
9. PCA does not depend on the units in which the variables are measured.
10. The first principal component is always the most useful direction for predicting an output.
11. For standardized data, the eigenvalues of the covariance matrix sum to the number of features.
12. For a truncated loading matrix $W_k$ ($k<n$), $W_kW_k^T=I$.
13. In the CNC example, PCA on the unstandardized data would give a PC1 dominated by cutting speed.

<details markdown="1">
<summary><strong>View Answer Key</strong></summary>

1. **False.** It is an average of squares.
2. **True.**
3. **False.** Only uncorrelated: $y=x^2$ on $[-1,0,1]$ has zero covariance.
4. **False.** It scales with the units; compare correlation coefficients instead.
5. **True.** $C=\tfrac{1}{m-1}X_c^TX_c$.
6. **True.** They are eigenvectors of a symmetric matrix.
7. **True.** $\vec q_j^TC\vec q_j=\lambda_j$.
8. **True.** $X_c=U\Sigma V^T$ gives $C=V\tfrac{\Sigma^2}{m-1}V^T$.
9. **False.** Rescaling one variable rotates the PCs; standardize first.
10. **False.** PCA ignores the output; high variance need not mean high relevance.
11. **True.** $\operatorname{tr}(C)=n$ because each diagonal entry is 1.
12. **False.** $W_k^TW_k=I_k$, but $W_kW_k^T$ is only a projection; that is why truncated reconstruction is approximate.
13. **True.** Its variance (1404) dwarfs the others; raw PC1 $\approx(0.95,\,0.0005,\,0.30)$ carries 99 % of the variance.

</details>

#### Practice question

**Q1.** For $x=[1,2,3]$ and $y=[2,4,6]$, find $C$, its eigenvalues and the explained variance of PC1.

<details markdown="1">
<summary><strong>View Answer</strong></summary>

$\bar x=2$, $\bar y=4$; deviations $[-1,0,1]$ and $[-2,0,2]$; $m-1=2$.

$$
C=\begin{bmatrix}1&2\\2&4\end{bmatrix},\qquad \lambda_1=5,\ \lambda_2=0,\qquad \vec q_1=\tfrac{1}{\sqrt5}\begin{bmatrix}1\\2\end{bmatrix}.
$$

PC1 explains 100 %: the points lie exactly on the line $y=2x$, so one dimension describes them completely ($\rho=1$).

</details>
