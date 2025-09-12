---
layout: post
title: "Active Subspaces 1: Finding Important Directions in High Dimensions"
date: 2025-09-09 17:30:00
description: "An intuitive introduction to Active Subspaces. We explain why high-dimensional models often hide lower-dimensional structures, connect the idea to ridge functions, and show simple toy examples that reveal how “important directions” emerge."
tags: [active-subspaces, dimensionality-reduction, ridge-function]
categories: [machine-scientist, physics]
code_diff: true
author: Jiashun Pang
thumbnail: assets/img/blog/activeSubspace1.png
---

# Active Subspaces 1: Finding Important Directions in High Dimensions

## 1. Motivation

In science and engineering, models often involve **dozens of variables**—geometric parameters, material properties, operating conditions. At first glance, every variable seems important. But experience tells us that **not all directions in parameter space matter equally**.

- Example: In fluid dynamics, instead of separately worrying about velocity, length scale, and viscosity, engineers combine them into a single dimensionless group—the Reynolds number.  
- This kind of reduction is powerful: a high-dimensional problem hides a **lower-dimensional structure**.

**Active Subspaces** is a systematic way to uncover these hidden structures. Instead of guessing which combinations matter, the method discovers “important directions” directly from the model.

---

## 2. The Core Idea

At its heart, the method assumes that a function can often be approximated as a **ridge function**:

$$
f(x) \approx g(W^T x),
$$

where:

- $x$ = original high-dimensional input,  
- $W^T x$ = a low-dimensional linear combination of variables,  
- $g$ = a simpler function defined on these few combinations.

👉 Translation: the model doesn’t care about each variable separately, but rather about a few **key mixtures**.

We won’t dive into the full mathematics yet—that’s Part 2. For now, keep in mind: **Active Subspaces = finding those mixtures automatically**.

---

## 3. A Toy Example

Consider a simple 2D function:

$$
f(x_1, x_2) = \sin(x_1 + 0.1 x_2).
$$

At first, it looks like both $x_1$ and $x_2$ are needed.  
But if you look closer, the function only depends on the **direction $x_1 + 0.1x_2$**. That’s the *important direction*.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/cocoJ-P/LinearAlgebra-in-MosaicX/blob/main/democode/ActiveSubspace.ipynb)

```python
import numpy as np
import matplotlib.pyplot as plt

X1, X2 = np.meshgrid(np.linspace(-3, 3, 100), np.linspace(-3, 3, 100))
F = np.sin(X1 + 0.1*X2)

plt.contourf(X1, X2, F, levels=20, cmap="viridis")
plt.xlabel("$x_1$")
plt.ylabel("$x_2$")
plt.title("Contours of $f(x_1, x_2) = sin(x_1 + 0.1 x_2)$")
plt.colorbar(label="f value")
plt.show()

```

What you’d see:

Contours aligned along diagonal stripes.

The model is essentially one-dimensional, even though it lives in 2D.

That diagonal is exactly the kind of structure Active Subspaces is built to reveal.

---

## 4. Takeaways

High-dimensional models often collapse to low-dimensional behavior.

Active Subspaces provides a **systematic tool** to find the directions where the function changes the most.

This is different from PCA: PCA only looks at data variation, while Active Subspaces looks at **function sensitivity**.

In **Part 2**, we’ll dive into the method itself: gradients, covariance matrices, and eigenvectors—the real machinery behind Active Subspaces.