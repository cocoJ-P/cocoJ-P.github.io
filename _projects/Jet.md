---
layout: page
title: Bucket–Jet Coupling 
description: "2022-2023&nbsp;(Incomplete Project)<br><br>A batch simulation study of Pelton turbine–jet atomization coupling was conducted with Basilisk and a custom modeling tool. The project was discontinued due to an incorrect technical approach."
img: assets/img/jetCover.png
importance: 3
category: completed
---

- **Date / Duration**: 2021–2022  
- **Contributors**: Jiashun Pang  
- **Status**: inactive  
- **Affiliation**: Key Laboratory for Mechanics in Fluid Solid Coupling Systems, Institute of Mechanics, Chinese Acadymy of Sciences

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Jet/title.png" title="title" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Jet–bucket interaction. Left top: jet impinging on a turbine bucket; left bottom: reference case without the bucket. Right: two-dimensional velocity field with adaptive mesh refinement, showing the jet evolution and interaction region.
</div>

---

# Project Summary

This project aimed to couple a parametric geometry generator for turbine buckets with the Basilisk CFD solver to study jet–bucket interaction under different design conditions. The workflow combined adaptive mesh refinement (AMR), Volume-of-Fluid (VOF) interface capturing, and parallel execution on supercomputers (OpenMP/MPI with batch submission). I implemented cases ranging from free jets to jet impingement on fixed and rotating buckets.

During the simulations, a key challenge emerged: STL-based bucket models suffered from numerical dissipation under AMR refinement, and moving-boundary cases required prohibitively small time steps. Although the project was not fully completed, the work established a pipeline for geometry–CFD coupling, highlighted fundamental difficulties in handling rotating STL geometries, and provided valuable experience for future improvements such as using signed distance functions or embedded boundary methods.

---

# 1. Motivation

The interaction between high-speed water jets and turbine buckets plays a central role in the efficiency and durability of impulse turbines. Accurate prediction of jet impingement and energy transfer is therefore critical for evaluating bucket designs and improving overall turbine performance. Traditional experimental studies provide valuable insights but are limited in terms of cost, scalability, and flexibility when exploring wide design spaces.

The motivation of this project was to establish a parametric design–simulation loop, where bucket geometries can be systematically generated within a predefined parameter space and directly coupled with CFD simulations. Such a framework would enable automated exploration of alternative designs, identification of performance trends, and ultimately the selection of optimal geometries for jet–bucket interaction.

---

# 2. Principles and Methods

2.1 **Bucket Modeling Tool** – brief review of the parametric geometry generator.
2.2 **CFD Methods** – adaptive mesh refinement (AMR), Volume-of-Fluid (VOF) interface capturing, finite volume discretization.
2.3 **Basilisk Solver** – features, strengths for multiphase jet simulation.
2.4 **Parallelization & HPC Usage** – OpenMP, MPI, batch submission via Bash scripts, supercomputer workflow.

---

# 3. Implemented Work

3.1 Inserting the bucket model into Basilisk and enabling motion.
3.2 Baseline case: free jet without boundaries.
3.3 Fixed bucket: jet impingement on a stationary geometry.
3.4 Rotating bucket: jet impingement under moving boundaries.

---

# 4. Challenges Encountered

* Dissipation and distortion of STL bucket model during AMR refinement.
* Severely restricted time step size in moving-boundary cases.

---

# 5. Attempted Solutions

* What approaches you tried (e.g., parameter tuning, mesh strategies).

---

# 6. Potential Solutions (not implemented at that time)

* Converting STL geometry to continuous signed distance function.
* Improved boundary representation (embedded boundaries, cut-cell methods).
* Alternative meshing or solver strategies.

---

# 7. Summary of Original Approach

* Restating the intended workflow from geometry generation → CFD coupling → parametric study.
* Clarifying what was achieved and what remained unfinished.

---

# 8. Reflections and Lessons Learned

* Personal growth in geometry–CFD coupling understanding.
* Realization of the gap between proposal-level ideas and actual implementation.
* Long-term value of the attempt despite incomplete results.

---

# 9. Reference