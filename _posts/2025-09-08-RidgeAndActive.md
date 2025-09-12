---
layout: post
title: "Ridge Functions, Response surface, Active Subspaces"
date: 2025-09-08
description: "An accessible exploration of key mathematical ideas—ridge functions, response surfaces, and active subspaces—that reveal hidden simplicity in complex physical systems."
tags: [dimensionality-reduction, ridge-function, response-surface, active-subspace, learning]
categories: [Mathematics, Physics, Machine-Scientist]
author: Jiashun Pang
code_diff: true
toc: true
featured: true
mathjax: true
chart:
  plotly: true
---


# 1. Introduction

* **Motivation:** Many physical systems involve a large number of parameters (e.g., density, viscosity, velocity, geometry). An engineer or scientist faces the “curse of dimensionality” when trying to model or interpret such systems.
* **Goal of this article:** Show how three mathematical ideas—ridge functions, response surfaces, and active subspaces—help reduce complexity, while still capturing the essence of the physics.
* **Why it matters:** These ideas underpin modern approaches like data-driven dimensional analysis (DDDA) and provide a foundation for my own work (e.g., MosaicX).

---

# 2. Ridge Functions: A Mathematical Lens on Dimensional Analysis

* **Concept:** In physics, dimensional analysis shows that many variables can often be collapsed into a few meaningful dimensionless groups. Ridge functions provide the mathematical expression of this idea.

* **Mathematical form:**

  $$
  f(x) = g(A^T x), \quad x \in \mathbb{R}^m, \; A \in \mathbb{R}^{m \times r}, \; r \ll m
  $$

  Here, the high-dimensional input $x$ (e.g., density, velocity, length, viscosity) is projected onto a lower-dimensional subspace through $A^T x$, and the essential relationship is captured by a simpler function $g(\cdot)$.

* **Physical interpretation:** Just as the drag coefficient can be reduced from depending on $(\rho, u, L, \mu)$ to depending on the single Reynolds number $\text{Re} = \rho u L / \mu$, ridge functions formalize the principle that **complex physical laws often act through a few hidden combinations of variables**.

* **Key message:** Ridge functions are not a physical law themselves, but a **mathematical abstraction of dimensional reduction**. They provide a bridge between the practice of dimensional analysis in physics and modern data-driven methods such as active subspaces or manifold learning.

---

# 3. Response Surfaces: Building Approximations

* **Concept:** A response surface is not a single technique, but a **general concept** referring to surrogate models that approximate complex input–output relationships. Under this umbrella fall many different methods—polynomial fits, Gaussian processes (Kriging), polynomial chaos expansions, and neural networks.

* **Position in modeling:** Unlike the intrinsic manifold defined by physical constraints (the “cause”), response surfaces are a **later construct**—built after we have sampled data from experiments or simulations. They serve as **engineering approximations** that mimic the system’s behavior without solving the governing equations directly.

* **Purpose:**

  * Provide a computationally cheap alternative to expensive simulations.
  * Smooth noisy experimental data into a usable functional form.
  * Enable derivative information (gradients, sensitivities) for optimization and design.

* **Connection:** While ridge functions describe the hidden low-dimensional structure imposed by physics, response surfaces are **tools we build on top of data** to approximate and explore those structures in practice.

* **Key message:** Response surfaces belong to the pragmatic, surrogate-modeling layer. They do not uncover the manifold itself but offer a tractable way to **approximate, interrogate, and exploit** the structure that the manifold encodes.

## Manifold vs. Response Surface

| Aspect         | **Manifold**                                                                                                    | **Response Surface**                                                                         |
| -------------- | --------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **Origin**     | Defined directly by **physical governing equations** (constraints, conservation laws, boundary conditions).     | Constructed **after sampling** data from experiments or simulations.                         |
| **Nature**     | A **geometric object** (subset in state space) that represents all admissible states of the system.             | A **surrogate model** (polynomial, GP, NN, PCE…) that approximates input–output relations.   |
| **Role**       | Captures the **intrinsic structure** of the system (low-dimensional, governed by DOF and constraints).          | Provides a **pragmatic approximation** for analysis, optimization, and prediction.           |
| **Guarantees** | Grounded in physics; always exists locally if the model is closed (implicit function theorem).                  | Depends on data quality and chosen surrogate method; no guarantee of global validity.        |
| **Resolution** | Potentially infinite detail (continuous object), but numerically accessed via discretization (patches atlas).   | Fixed functional form once trained; smooths over noise and expensive sampling.               |
| **Use cases**  | Understanding singularities, bifurcations, and domains of validity; guiding where explicit functions may exist. | Reducing computational cost; sensitivity analysis; optimization; uncertainty quantification. |
| **Status**     | The **cause** (why variables reduce, why hidden structure exists).                                              | The **effect** (how we approximate or exploit that structure in practice).                   |

---

# 4. Active Subspaces: Finding Important Directions

* **Concept:** In high-dimensional models, not all variables influence the output equally. **Active subspaces** provide a systematic way to uncover the most influential combinations of variables—those directions in input space along which the output varies the most.

* **Method:**

  1. Compute or approximate gradients of the target function $f(x)$.
  2. Form the **gradient covariance matrix**:

     $$
     C = \int (\nabla f(x))(\nabla f(x))^T \,\rho(x)\, dx
     $$

     where $\rho(x)$ is a probability density describing the input distribution.
  3. Perform eigen-decomposition of $C$.

     * The eigenvectors associated with the largest eigenvalues span the **active subspace**.
     * Directions corresponding to small eigenvalues are deemed inactive (output is nearly constant along them).

* **Interpretation:**

  * Active subspaces compress the input space into a lower-dimensional set of **dominant directions**.
  * This aligns with the ridge function idea: instead of depending on each raw input variable, the system depends mainly on a few linear combinations.
  * By projecting data onto the active subspace, one can build simpler surrogate models, perform sensitivity analysis, or guide experimental design.

* **Example in physics:**

  * In fluid dynamics, drag coefficient $C_d$ appears to depend on multiple raw inputs $(\rho, u, L, \mu)$.
  * Active subspace analysis reveals that the dominant direction corresponds to the **Reynolds number**, effectively reducing the dimensionality from four variables to one.

* **Key message:** Active subspaces bridge physics and data: they turn the gradient structure of a model into a practical recipe for **dimension reduction**, making high-dimensional problems tractable without losing the essential behavior.

Ref:

{{< linkcard
    url="https://openai.com"
    title="OpenAI"
    desc="人工智能研究与应用 | 官方网站"
    image="https://assets-global.website-files.com/650.../openai-og.jpg"
>}}


---

# 5. How They Fit Together

* Ridge functions → conceptual lens: “outputs depend on combinations, not all inputs.”
* Response surfaces → computational tool: “approximate the system so we can analyze it.”
* Active subspaces → discovery mechanism: “extract the most relevant combinations.”

---

## 6. Implications for Physical Modeling

* **For classical dimensional analysis:** Provides a way to rank and select among the many possible dimensionless groups.
* **For data-driven science:** Enables automated discovery of reduced models from simulation or experimental data.
* **For my perspective (MosaicX):** Beyond identifying combinations, we also need to explore where these combinations fail—singular points, bifurcations, and boundaries of validity.

---

# 7. Conclusion

* **Takeaway:** High-dimensional physical problems often have hidden low-dimensional structure. Ridge functions, response surfaces, and active subspaces are powerful tools to uncover it.
* **Next steps:** Future posts will dive deeper into specific algorithms, case studies (e.g., laminar/turbulent flows), and how these methods integrate into a broader framework for automated scientific discovery.


