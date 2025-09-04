---
layout: page
title: Pelton Turbine Parametric Modeling Tool
description: "2021-2022<br><br>A MATLAB GUI tool for parametric modeling of Pelton turbines using Bézier curves."
img: assets/img/peltonCover.png
importance: 2
category: completed
---

- **Date / Duration**: 2021–2022  
- **Contributors**: Jiashun Pang  
- **Status**: Completed  

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/app31.png" title="app31" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/app32.png" title="app32" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/app33.png" title="app33" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The third version of the Pelton Turbine Parametric Modeling Tool, developed entirely in MATLAB GUI.
</div>

---

# 1. Motivation 
This engineering project aims to develop a **parametric modeling tool** for designing a new *Pelton turbine bucket*, with design parameters **extending far beyond existing models**, in pursuit of **higher efficiency and reliability**. In this scenario, the **lack of both historical datasets and experimental data** makes traditional data-driven evaluation or benchmarking infeasible. Rather than keeping the geometry fixed and only tuning CFD conditions, our approach couples **parametric geometry modeling with CFD simulations**, enabling both domains to co-evolve through parameter variation. To this end, we are exploring an **automated workflow** that integrates the modeling stage with CFD into a **feedback loop**, iteratively refining the design until an **optimized model emerges**. This effort also represents the initial step toward my long-term vision — the development of a **“Machine Scientist.”**

---

# 2. Principle

## 2.1 Bernstein basis function

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/Bernstein1.png" title="Bernstein1" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/Bernstein2.png" title="Bernstein2" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: A quadratic Bézier curve defined by two endpoints and two control points.
Right: The corresponding Bernstein basis functions of degree 3, which govern the influence of each control point along the curve.
</div>

The construction of a Bézier curve is based on **Bernstein basis functions**, which determine how control points influence the final curve.  
For a cubic Bézier curve, the parametric representation can be written as:

$$
Q_u=\sum^3_{i=0}p_i B_i(u), \qquad B_i(u)=\frac{3!}{i!(3-i)!}u^i(1-u)^{3-i}
$$

where $Q_u$ is a point on the curve at parameter $u \in [0,1]$, $p_i$ are the control points, and $B_i(u)$ are the Bernstein polynomials of degree 3.

Explicitly, the four basis functions are:

$$
B_0(u)=(1-u)^3, \qquad B_1(u)=3u(1-u)^2, \qquad B_2(u)=3u^2(1-u), \qquad B_3(u)=u^3
$$

These functions have the following important properties:

- **Non-negativity**: $B_i(u) \geq 0$ for all $u \in [0,1]$.  
- **Partition of unity**: $\sum_{i=0}^3 B_i(u) = 1$, ensuring the curve always lies within the convex hull of its control points.  
- **Local control**: Each $B_i(u)$ determines the influence of control point $p_i$ along the curve, as illustrated in the right figure.  

Together, these properties guarantee that Bézier curves are smooth, stable, and intuitive to design using control points, as shown in the left figure.


## 2.2 Bézier surface
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/BezierSurface.png" title="bTheory1" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    From left to right: the control mesh, the intermediate polygonal surface, and the final smooth Bézier surface of the Pelton turbine bucket.
</div>

In this project, the bucket geometry is generated using bicubic Bézier surfaces, which provide a flexible yet mathematically rigorous representation of complex curves. The surface is defined by a grid of control points $p_{ij}$, with the shape interpolated through Bernstein basis functions $B_{i}(u)$ and $B_{j}(v)$:

$$
    Q(u, v) = \sum^{3}_{i=0} \sum^{3}_{j=0} p_{ij}B_{i}(u)B_{j}(v)
$$

Here, u and v are parametric coordinates, and the basis functions ensure smooth transitions across the surface. The figure above illustrates this construction process: starting from the control mesh, passing through a coarse polygonal form, and resulting in the final smooth bucket surface suitable for turbine flow simulations.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/bTheory1.png" title="bTheory1" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Illustration of a Bézier surface: (a) control grid defined by points $p_{ij}$ (b) the resulting smooth surface generated from the control points. As introduced earlier for Bézier curves, the same principle extends naturally to surfaces.
</div>

---

# 3 Application to bucket design

In this chapter, the Bézier surface formulation introduced earlier is applied to the practical design of a Pelton turbine bucket. The process is organized into three stages: the translation of Bézier surfaces into bucket geometry, the development of a MATLAB-based parametric GUI, and the post-processing of the generated models for further analysis and simulation.

## 3.1 From Bézier surfaces to bucket geometry

In this subsection, we apply the Bézier surface approach to the design of a Pelton turbine bucket. Specifically, we adopt the control-point layout reported by Vessaz et al. (2017) and embed it directly into our workflow. Rather than pursuing mathematical optimization, the literature-provided control points are used as the geometric foundation, allowing us to validate our parametric modeling tool and establish a consistent starting point for subsequent CFD coupling and performance analysis.

The construction process follows a clear sequence. First, a control-point framework is established to define the basic geometric skeleton of the bucket. Next, a Bézier surface is generated from these points, producing a smooth and mathematically consistent representation. To obtain the full bucket geometry, the framework is mirrored, ensuring symmetry across the bucket structure. Finally, the complete bucket surface is generated using the Bézier formulation, yielding a continuous and well-defined geometry suitable for engineering applications.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/try1.png" title="try1" class="img-fluid rounded z-depth-1" style="height:200px; object-fit:contain;" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/try2.png" title="try2" class="img-fluid rounded z-depth-1" style="height:200px; object-fit:contain;" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/try3.png" title="try3" class="img-fluid rounded z-depth-1" style="height:200px; object-fit:contain;" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PModelling/try4.png" title="try4" class="img-fluid rounded z-depth-1" style="height:200px; object-fit:contain;" %}
    </div>
</div>
<div class="caption">
    From left to right: (1) construction of the control-point framework, (2) generation of the Bézier surface, (3) mirroring of the framework to form the bucket structure, and (4) generation of the final bucket surface using Bézier representation.
</div>

## 3.2 Development of the MATLAB GUI tool

To facilitate practical use, a dedicated MATLAB GUI was developed to streamline the parametric design process. The interface allows users to adjust design parameters, and visualize the resulting bucket geometry in real time. This tool provides a more intuitive workflow, enabling rapid prototyping and iterative improvements without requiring direct coding or manual surface construction.

## 3.3 Geometry post-processing

After the bucket geometry is generated, post-processing is performed to prepare the model for analysis and simulation. This stage includes surface smoothing, mesh generation, and the export of geometry in formats compatible with CFD solvers. The post-processing step ensures that the designed bucket can be seamlessly embedded into a simulation pipeline for hydraulic performance evaluation.

---

# 4. APP迭代

---

# 5. Conclusion and 心得体会

---

# 6. References

1. Vessaz, C., Andolfatto, L., Avellan, F., & Tournier, C. (2017). *Toward design optimization of a Pelton turbine runner*. Structural and Multidisciplinary Optimization, 55(1). https://doi.org/10.1007/s00158-016-1465-7


    ---
    layout: page
    title: project
    description: a project with a background image
    img: /assets/img/12.jpg
    ---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Caption photos easily. On the left, a road goes through a tunnel. Middle, leaves artistically fall in a hipster photoshoot. Right, in another hipster photoshoot, a lumberjack grasps a handful of pine needles.
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    This image can also have a caption. It's like magic.
</div>

You can also put regular text between your rows of images.
Say you wanted to write a little bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, _bled_ for your project, and then... you reveal its glory in the next row of images.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>

The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}

```html
<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
```

{% endraw %}
