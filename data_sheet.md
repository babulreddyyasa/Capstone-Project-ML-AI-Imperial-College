# BBO Capstone Project Dataset Datasheet

## 1. Motivation

This dataset was created to support a black-box optimization (BBO) capstone project focused on learning how to optimize unknown objective functions under a limited evaluation budget. The dataset captures the sequence of query points proposed during the project and the scalar outputs returned by each black-box function.

The dataset supports the following tasks:

- studying exploration versus exploitation in Bayesian optimization;
- comparing query strategies across functions with different dimensionalities and response surfaces;
- identifying promising regions that maximize an objective when gradients and closed-form equations are unavailable;
- documenting the decision trail of an iterative optimization workflow.

The project is educational and experimental in nature. It is intended to help develop intuition for surrogate modeling, uncertainty-aware search, and query-efficient optimization in settings that resemble contamination-source detection, hyperparameter tuning, and other expensive-search problems.

## 2. Composition

The dataset contains observations for 8 black-box functions. Each observation consists of:

- an input vector of continuous values, typically bounded to the range `[0, 1]`;
- a single scalar output value to be maximized.

The repository stores the data in two forms:

- initial seed data for each function in `function_i/initial_inputs.npy` and `function_i/initial_outputs.npy`;
- sequentially appended project submissions in [inputs.txt](/Users/babulreddyyasa/Capstone-Project-ML-AI-Imperial-College/inputs.txt:1) and [outputs.txt](/Users/babulreddyyasa/Capstone-Project-ML-AI-Imperial-College/outputs.txt:1).

Current baseline seed sizes in the repository are:

- Function 1: 10 input-output pairs, 2 input dimensions
- Function 2: 10 input-output pairs, 2 input dimensions
- Function 3: 15 input-output pairs, 3 input dimensions
- Function 4: 30 input-output pairs, 4 input dimensions
- Function 5: 20 input-output pairs, 4 input dimensions
- Function 6: 20 input-output pairs, 5 input dimensions
- Function 7: 30 input-output pairs, 6 input dimensions
- Function 8: 40 input-output pairs, 8 input dimensions

The appended logs in `inputs.txt` and `outputs.txt` contain additional rounds of submitted candidate points and corresponding outputs for all 8 functions.

Format details:

- `.npy` files store NumPy arrays for direct loading into Python;
- `inputs.txt` stores one submitted batch per line, with one array per function;
- `outputs.txt` stores the corresponding scalar results for the same batch order.

Known gaps and limitations:

- the dataset is small and designed for iterative optimization rather than large-scale supervised learning;
- timestamps are not stored with each query;
- metadata describing which acquisition function or notebook version generated each query is not fully recorded in the raw files;
- the internal ground-truth definitions of the black-box functions are intentionally unavailable.

## 3. Collection Process

The dataset was generated through an iterative query-and-evaluate process during the capstone project. For each function, the workflow began with an initial seed set and then added new candidate points proposed by notebook-based search strategies.

The main query-generation strategy combined:

- exploratory sampling to cover the search space and detect promising regions;
- Bayesian optimization using Gaussian Process surrogate models;
- acquisition-based selection, especially Upper Confidence Bound (UCB), to balance high predicted reward with model uncertainty;
- local refinement around currently strong points through small perturbations and heuristic hill-climbing.

Different functions required somewhat different emphasis. Lower-dimensional problems supported denser local search and visualization, while higher-dimensional problems relied more heavily on surrogate predictions and heuristic feature inspection.

The dataset was collected over the course of the capstone workflow rather than through a single fixed collection event. In repository form, the time frame is represented as ordered rounds of submissions in the text logs, but exact timestamps are not included.

## 4. Preprocessing and Uses

Preprocessing and representation steps include:

- storing the initial data as NumPy arrays;
- formatting later query rounds into plain-text array logs;
- combining initial arrays with appended query histories inside the notebooks before fitting surrogate models;
- treating all functions as maximization problems, even when some outputs are negative-valued.

No heavy preprocessing such as normalization, imputation, or label cleaning is evident in the repository-level dataset files. Most transformations happen at notebook runtime when data is loaded, concatenated, and passed into models.

Intended uses:

- teaching and demonstrating black-box optimization workflows;
- testing Bayesian optimization and heuristic search strategies;
- comparing behavior across objective functions with different dimensions and scales;
- documenting query histories for report writing and project reflection.

Inappropriate uses:

- benchmarking claims about real-world contamination, radiation, chemistry, or safety-critical systems;
- training production-grade models for deployment in scientific or medical settings;
- inferring causal properties of any real physical process;
- using the dataset as if it were a comprehensive, independently validated benchmark.

Because the functions are synthetic or opaque black boxes in an educational setting, outputs should be interpreted as optimization signals rather than real measurements from a governed operational system.

## 5. Distribution and Maintenance

The dataset is currently available through this project repository, primarily in the `function_*` directories and the root-level `inputs.txt` and `outputs.txt` files.

Based on the repository contents currently available, no explicit standalone dataset license or formal terms of use are documented. Unless additional course guidance or repository licensing information exists elsewhere, the safest interpretation is that the dataset is intended for coursework, personal study, and project demonstration within the capstone context.

Maintenance is currently project-maintainer driven. In practice, this means the repository owner or capstone author is responsible for:

- updating the query logs;
- maintaining notebook compatibility with the stored data files;
- documenting new rounds of optimization;
- clarifying licensing, sharing permissions, and versioning if the dataset is distributed further.

Recommended maintenance improvements:

- add a repository license that explicitly covers the dataset;
- add version numbers or dates for each submission round;
- record the generating notebook, acquisition setting, and rationale for each query;
- freeze a final release snapshot for reproducibility.

## 6. Summary

This dataset is a compact, iterative record of a black-box optimization capstone project. It is best understood as an optimization trace dataset: a set of seed evaluations plus sequential candidate queries and outcomes across 8 functions of varying dimensionality. Its value lies in supporting learning, experimentation, and reflection on query-efficient optimization under uncertainty, rather than serving as a large, production-ready benchmark dataset.
