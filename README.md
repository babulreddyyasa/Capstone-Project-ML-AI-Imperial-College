# Black-Box Optimization (BBO) Capstone Project

## Section 1: Project Overview

The BBO capstone project focuses on optimizing black-box functions whose internal mechanics are unknown or expensive to evaluate. These functions simulate real-world problems such as detecting contamination sources, tuning ML hyperparameters, or optimizing chemical processes. The overall goal is to identify input configurations that maximize outputs while using as few queries as possible. This project is highly relevant in practical ML and engineering scenarios, where experiments are costly, outputs may be noisy, and the system is partially observed. By engaging with this problem, I develop skills in decision-making under uncertainty, exploration-exploitation strategies, and interpreting complex, high-dimensional data, all of which are directly applicable to careers in data science, optimization, and applied ML research.

---

## Section 2: Inputs and Outputs

Each function receives a multi-dimensional input array and returns a single scalar output representing performance or signal strength.

- **Inputs:**  
  - Format: `[x1, x2, ..., xd]` where `d` varies by function (2D–8D).  
  - Constraints: Continuous values, typically in the range `[0, 1]`.  
  - Examples:  
    - Function 1 (2D): `[0.731024, 0.732999]`  
    - Function 6 (5D): `[0.45, 0.30, 0.15, 0.50, 0.25]`  

- **Outputs:**  
  - A single scalar value reflecting function performance or signal strength.  
  - Examples:  
    - Function 1: `7.71e-16` (near a contamination source)  
    - Function 6: `-0.012` (negative of loss, framed for maximization)  

---

## Section 3: Challenge Objectives

The primary objective is to **maximize each function’s output** while adhering to practical constraints:  

- **Limited queries:** Only a small number of function evaluations are allowed relative to the search space.  
- **Unknown function structure:** The relationships between inputs and outputs are opaque.  
- **Noisy or sparse outputs:** Some functions produce highly variable or mostly zero outputs, making peaks difficult to detect.  

Secondary considerations include computational efficiency and handling high-dimensional input spaces, particularly for Functions 6–8. Overall, the challenge is to locate the **global maximum or strong local maxima** efficiently, minimizing wasted queries.

---

## Section 4: Technical Approach

The approach integrates **exploration**, **exploitation**, and **surrogate modeling**:

1. **Exploration:**  
   - Early queries are spread randomly or via low-discrepancy sampling to cover the input space and locate regions with non-zero or high outputs.  
   - Essential for sparse or noisy functions like Function 1 and Function 2.

2. **Exploitation:**  
   - Once a promising region is detected, small local perturbations refine the maximum.  
   - Step sizes and directions are chosen based on prior observations, enabling controlled hill-climbing near peaks.

3. **Modeling:**  
   - **Gaussian Processes (GPs)** serve as surrogate models to predict outputs and estimate uncertainty.  
   - Acquisition functions like **Upper Confidence Bound (UCB)** balance exploration of high-uncertainty regions with exploitation of high-value areas.  
   - Thresholding outputs allows **soft-margin or kernel SVMs** to classify high vs low regions, capturing non-linear boundaries, though regression remains necessary for precise maxima.

4. **Heuristics & Iterative Improvement:**  
   - Directional probing along individual features helps test local gradients.  
   - Irrelevant features or dimensions are noted for potential dimensionality reduction.  
   - Strategies evolve as new outputs are observed, refining both surrogate predictions and heuristic choices.

This approach is effective because it combines **uncertainty-aware modeling**, **local exploration heuristics**, and **flexible adaptation** to various function types, from low-dimensional sparse peaks to high-dimensional complex landscapes. It mirrors real-world optimization, where incomplete knowledge, limited experiments, and multi-dimensional interactions are common.

---

## Section 5: Learning Outcomes

- Developed intuition for **exploration vs exploitation** in black-box optimization.  
- Learned to **incorporate uncertainty** into decision-making using surrogate models.  
- Gained experience interpreting **sparse, non-linear, and high-dimensional functions**.  
- Improved skills in **feature relevance assessment, visualization, and incremental optimization**, applicable to real-world data science challenges.  
- Prepared to make decisions under uncertainty, combining **heuristics with model-based predictions**.

---

## Section 6: Tools and Visualization

- **Python Libraries:** NumPy, SciPy, Matplotlib, Seaborn, scikit-learn  
- **Visualization Techniques:**  
  - PCA projections for high-dimensional input points  
  - 2D/3D scatter plots  
  - Pairplots and parallel coordinates to observe trends and interactions  
- Query selection combines **visual intuition** and **model predictions**, refining maxima efficiently.  

---

## Section 7: Future Work

- Implement **batch query strategies** to optimize multiple points in parallel.  
- Explore **kernelized surrogate models** for highly non-linear, high-dimensional surfaces.  
- Incorporate automated **feature selection** and **dimensionality reduction** for Functions 6–8.  
- Combine Bayesian Optimization with **evolutionary algorithms** to handle rugged or multi-modal landscapes.  
