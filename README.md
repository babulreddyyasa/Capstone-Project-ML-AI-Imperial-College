# Black-Box Optimization (BBO) Capstone Project

This repository contains my capstone work on black-box optimization across 8 unknown objective functions. The project focuses on query-efficient maximization: given only input-output evaluations, the goal is to propose the next best point to sample while balancing exploration and exploitation.

The workflows are implemented in function-specific Jupyter notebooks and supported by shared query logs in `inputs.txt` and `outputs.txt`.

## Project Documentation

- [Dataset Datasheet](dataset_datasheet.md)
- [Model Card for the BBO Optimisation Approach](model_card.md)

## Project Summary

The project investigates how Bayesian optimization and related heuristics can be used to maximize black-box functions with limited evaluation budgets. Across the repository, the approach combines:

- Gaussian Process surrogate models
- acquisition functions such as Upper Confidence Bound (UCB) and Expected Improvement (EI)
- global random exploration
- local perturbation around high-performing regions
- function-specific heuristics for different dimensions and response surfaces

The functions vary in dimension from 2D to 8D and represent different optimization settings, including sparse-response search problems such as contamination-source detection and more structured high-dimensional maximization tasks.

## Repository Structure

- `function_1/` to `function_8/`
  Each folder contains:
  - `function_i.ipynb`: notebook used to analyze that function and generate the next candidate point
  - `initial_inputs.npy`: initial seed inputs for that function
  - `initial_outputs.npy`: initial seed outputs for that function
- `inputs.txt`
  Sequential log of submitted candidate inputs across all 8 functions
- `outputs.txt`
  Sequential log of returned scalar outputs corresponding to `inputs.txt`
- `dataset_datasheet.md`
  Datasheet describing the dataset, collection process, intended uses, and maintenance considerations
- `model_card.md`
  Model card describing the optimization approach, performance, assumptions, and limitations

## Data Format

Each function is treated as a maximization problem.

- Inputs are continuous vectors, typically bounded to `[0, 1]`
- Outputs are scalar objective values
- Initial seed observations are stored as NumPy arrays
- Additional query rounds are stored as ordered plain-text logs in `inputs.txt` and `outputs.txt`

Function dimensionalities:

- Function 1: 2D
- Function 2: 2D
- Function 3: 3D
- Function 4: 4D
- Function 5: 4D
- Function 6: 5D
- Function 7: 6D
- Function 8: 8D

## Optimisation Approach

The notebooks follow the same high-level loop:

1. load the initial seed data for one function;
2. append any previously submitted query points and observed outputs;
3. fit a surrogate model to the available data;
4. score candidate points using an acquisition rule or search heuristic;
5. propose the next point expected to improve the objective.

The approach evolved across the project rather than staying fixed. Early rounds emphasized broader exploration, while later rounds focused more on exploiting promising regions discovered by the surrogate model.

## Current Performance Snapshot

Using the 10 logged rounds in `inputs.txt` and `outputs.txt`, the best observed submitted values are:

- Function 1: `0.005716883885302281`
- Function 2: `0.6219564059776775`
- Function 3: `-0.009153765203187628`
- Function 4: `-28.914094514315888`
- Function 5: `2777.805513886249`
- Function 6: `-0.5707448070355233`
- Function 7: `1.0564175188888865`
- Function 8: `9.605274086169`

These results show the approach can produce strong gains on some functions, while performance is more mixed on others. A fuller interpretation is documented in the model card.

## How To Use

To work with a function notebook:

1. open one of the notebooks in `function_1/` to `function_8/`;
2. load the initial `.npy` data for that function;
3. read prior project submissions from `inputs.txt` and `outputs.txt`;
4. run the notebook cells to fit the current surrogate model;
5. inspect the proposed next query point.

The notebooks use common Python scientific libraries, including NumPy, SciPy, Matplotlib, and scikit-learn.

## Scope and Limitations

This repository is an educational capstone project, not a production optimization system. The black-box functions are intentionally opaque, the logged dataset is small, and some notebook choices are function-specific rather than standardized across all tasks.

The project is best used as:

- a record of iterative black-box optimization experiments
- a demonstration of Bayesian optimization ideas
- a basis for reflection on exploration, exploitation, and surrogate modeling

It should not be treated as a validated benchmark for safety-critical real-world decision-making.

## Future Improvements

- standardize acquisition and logging settings across all notebooks
- add per-round metadata such as timestamps and rationale for each query
- compare multiple acquisition strategies more systematically
- add reproducibility guidance for rerunning the full optimization workflow
