---
layout: post
title: "Active Subspaces 2: Gradients, Covariances, and Eigenvectors"
date: 2025-09-14 02:18:20
description: A step-by-step dive into the mathematics of Active Subspaces. From gradient covariance matrices to eigen-decomposition, we show how to extract dominant directions, compare the method with PCA, and discuss practical issues like gradient computation.
tags: [active-subspaces, dimensionality-reduction, ridge-function]
categories: [machine-scientist, physics]
code_diff: true
author: Jiashun Pang
thumbnail: assets/img/blog/activeSubspace1.png
---

# Active Subspaces 2: Gradients, Covariances, and Eigenvectors

In [Part 1](Active-Subspaces-1), we introduced the **intuition** of Active Subspaces: high-dimensional functions often depend on a few “important mixtures” of inputs rather than every variable separately.  

Now let’s roll up our sleeves and see how to **find those mixtures mathematically**.

---

## 1. Gradients as Sensitivity Maps

The starting point is the **gradient** of our function $f(x)$.

- $x \in \mathbb{R}^m$: high-dimensional input  
- $f: \mathbb{R}^m \to \mathbb{R}$: scalar output  

The gradient is:

$$
\nabla f(x) = 
\begin{bmatrix}
\frac{\partial f}{\partial x_1}(x) \\
\vdots \\
\frac{\partial f}{\partial x_m}(x)
\end{bmatrix}.
$$

👉 Interpretation: the gradient tells us **how sensitive $f$ is to each variable at point $x$**.  
If a component of $\nabla f$ is large, small changes in that variable have a big effect.

---

## 2. Covariance of Gradients

Instead of looking at gradients at individual points, Active Subspaces looks at the **average behavior of gradients** across the whole parameter space.  

Define the **gradient covariance matrix**:

$$
C = \int (\nabla f(x))(\nabla f(x))^T \, \rho(x) \, dx,
$$

where $\rho(x)$ is a probability density describing how we sample the input space.  

- $C$ is an $m \times m$ symmetric positive semi-definite matrix.  
- Each entry measures how pairs of variables jointly influence the function.  

👉 Intuition: $C$ is like a “global sensitivity matrix” — it summarizes how directions in $x$-space matter **on average**.

---

## 3. Eigen-Decomposition: Extracting Directions

Since $C$ is symmetric, we can diagonalize it:

$$
C = W \Lambda W^T,
$$

- $W = [w_1, w_2, \dots, w_m]$: orthonormal eigenvectors (directions in input space)  
- $\Lambda = \mathrm{diag}(\lambda_1, \dots, \lambda_m)$: eigenvalues (importance of each direction)  

We sort eigenvalues:

$$
\lambda_1 \ge \lambda_2 \ge \dots \ge \lambda_m \ge 0.
$$

👉 Big picture:

- Large $\lambda_i$: the function is **sensitive** along direction $w_i$.  
- Small $\lambda_i$: the function is nearly **flat** along direction $w_i$.  

This is where the “active subspace” lives: the span of eigenvectors with large eigenvalues.

---

## 4. Comparison with PCA

At first glance, this looks like **PCA (Principal Component Analysis)**.  

- PCA: finds directions of **largest variance in data $x$**.  
- Active Subspaces: finds directions of **largest variation in the function $f(x)$**.  

So:

- PCA cares only about how inputs are distributed.  
- Active Subspaces explicitly cares about how **outputs respond to inputs**.

That’s why Active Subspaces often produces more **physically meaningful combinations**.

---

## 5. Practical Issues

### (a) How to Compute Gradients?

- **Analytical**: if $f(x)$ is simple or symbolic, compute derivatives directly.  
- **Automatic Differentiation**: use tools like TensorFlow, JAX, or PyTorch.  
- **Finite Differences**: approximate via small perturbations — but beware of noise.  

### (b) How Many Samples?

Since $C$ is defined as an integral, we usually approximate it via Monte Carlo:

$$
\hat{C} = \frac{1}{N} \sum_{i=1}^N (\nabla f(x_i))(\nabla f(x_i))^T,
$$

where $x_i \sim \rho(x)$.  
Rule of thumb: the higher the dimension, the more samples you need.

### (c) Choosing Subspace Dimension

Look at the **eigenvalue spectrum**:

- If $\lambda_k \gg \lambda_{k+1}$, then $k$ is a natural cutoff.  
- The first $k$ eigenvectors span the active subspace.

---

## 6. A Toy Example (Revisited)

Let’s go back to the function:

$$
f(x_1, x_2) = \sin(x_1 + 0.1 x_2).
$$

1. Compute gradients:

$$
\nabla f = 
\begin{bmatrix}
\cos(x_1 + 0.1 x_2) \\
0.1 \cos(x_1 + 0.1 x_2)
\end{bmatrix}.
$$

2. Form $C$ by averaging over a uniform distribution on $[-\pi, \pi]^2$.  
   You’ll find $C$ has one large eigenvalue (direction $\propto [1, 0.1]^T$), and one tiny eigenvalue.  

👉 This confirms the intuition: the function is effectively **1D**.

---

## 7. Takeaways

- **Gradients → covariance → eigen-decomposition** is the pipeline.  
- Eigenvectors with large eigenvalues = **active directions**.  
- Compared to PCA, Active Subspaces is **function-aware**.  
- In practice, everything hinges on good gradient estimation and enough sampling.

In **Part 3**, we’ll explore **applications**: how Active Subspaces shows up in fluid dynamics, aerospace design, and uncertainty quantification.

