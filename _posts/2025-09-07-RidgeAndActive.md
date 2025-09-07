---
layout: post
title: "From Ridge Functions to Active Subspaces: Dimensionality Reduction in High-Dimensional Physical Problems"
date: 2025-09-07
description: "An accessible exploration of key mathematical ideas—ridge functions, response surfaces, and active subspaces—that reveal hidden simplicity in complex physical systems."
tags: [dimensionality-reduction, ridge-function, response-surface, active-subspace, learning]
categories: [Mathematics, Physics, Machine-Scientist]
author: Jiashun Pang
toc: true
mathjax: true
chart:
  plotly: true
---


## 1. Introduction

* **Motivation:** Many physical systems involve a large number of parameters (e.g., density, viscosity, velocity, geometry). An engineer or scientist faces the “curse of dimensionality” when trying to model or interpret such systems.
* **Goal of this article:** Show how three mathematical ideas—ridge functions, response surfaces, and active subspaces—help reduce complexity, while still capturing the essence of the physics.
* **Why it matters:** These ideas underpin modern approaches like data-driven dimensional analysis (DDDA) and provide a foundation for my own work (e.g., MosaicX).

---

## 2. Ridge Functions: Hidden Simplicity in Complex Models

* **Intuition:** Even when a model has many input variables, the output often depends only on a few meaningful combinations.
* **Mathematical form:**

  $$
  f(x) = g(A^T x), \quad x \in \mathbb{R}^m, \; A \in \mathbb{R}^{m \times r}, \; r \ll m
  $$
* **Example:** The drag coefficient depending only on the Reynolds number, which itself combines density, velocity, length, and viscosity.
* **Key message:** Ridge functions are a natural way to understand why dimensionless groups are powerful in physics.

---

## 3. Response Surfaces: Building Approximations

* **Definition:** A surrogate model (polynomial fit, Gaussian process, neural net) that approximates a complex input–output relationship.
* **Purpose:**

  * Smooth noisy or expensive data.
  * Provide derivatives or gradients for analysis.
* **Connection:** Response surfaces are often used as a practical tool to explore ridge-like structures in real data.

---

## 4. Active Subspaces: Finding Important Directions

* **Problem:** How to discover which combinations of variables matter most?
* **Method:**

  * Compute or approximate gradients of the function.
  * Form the covariance matrix of gradients:

    $$
    C = \int (\nabla f(x))(\nabla f(x))^T \rho(x) dx
    $$
  * Eigen-decomposition of \$C\$ → leading eigenvectors = active subspace.
* **Interpretation:** Active subspaces identify dominant directions in the input space where the function varies the most.
* **Example in physics:** Identifying that flow resistance is primarily governed by Reynolds number (rather than each raw variable separately).

---

## 5. How They Fit Together

* Ridge functions → conceptual lens: “outputs depend on combinations, not all inputs.”
* Response surfaces → computational tool: “approximate the system so we can analyze it.”
* Active subspaces → discovery mechanism: “extract the most relevant combinations.”

---

## 6. Implications for Physical Modeling

* **For classical dimensional analysis:** Provides a way to rank and select among the many possible dimensionless groups.
* **For data-driven science:** Enables automated discovery of reduced models from simulation or experimental data.
* **For my perspective (MosaicX):** Beyond identifying combinations, we also need to explore where these combinations fail—singular points, bifurcations, and boundaries of validity.

---

## 7. Conclusion

* **Takeaway:** High-dimensional physical problems often have hidden low-dimensional structure. Ridge functions, response surfaces, and active subspaces are powerful tools to uncover it.
* **Next steps:** Future posts will dive deeper into specific algorithms, case studies (e.g., laminar/turbulent flows), and how these methods integrate into a broader framework for automated scientific discovery.

