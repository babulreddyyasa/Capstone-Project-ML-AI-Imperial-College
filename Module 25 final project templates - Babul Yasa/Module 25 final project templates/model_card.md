# Model Card

## Model Description

**Input:**  
The input is a continuous vector representing one candidate query for a black-box function. Depending on the task, the dimensionality ranges from 2 to 8, with each feature typically bounded to `[0, 1]`.

**Output:**  
The output is a single scalar objective value returned by the hidden function. The optimisation goal is to maximize this value using as few evaluations as possible.

**Model Architecture:**  
This project does not use one fixed end-to-end predictive model. Instead, it uses a sequential optimisation workflow built around Gaussian Process surrogate models with RBF or Matérn kernels, plus acquisition-guided search. In each round, the workflow:

1. combines the initial seed data with all submitted historical queries;
2. fits a Gaussian Process surrogate model;
3. scores candidate points using Upper Confidence Bound or Expected Improvement;
4. proposes the next query using a mix of exploration and local exploitation.

The later notebooks rely more heavily on local trust-region search around the top few observed points, especially for higher-dimensional functions.

## Performance

The main performance metric is the best observed objective value for each function after 13 submitted rounds.

Final best observed values:

- Function 1: `0.019275867963356643` at `[0.650720, 0.662477]`
- Function 2: `0.6219564059776775` at `[0.680214, 0.502871]`
- Function 3: `-0.0010417641626487369` at `[0.355806, 0.576302, 0.482483]`
- Function 4: `-1.4677350476611966` at `[0.485150, 0.460770, 0.413452, 0.339324]`
- Function 5: `3651.0406835289377` at `[0.205624, 0.969307, 0.967528, 1.000000]`
- Function 6: `-0.5707448070355233` at `[0.884317, 0.312451, 0.668732, 0.699154, 0.156208]`
- Function 7: `1.470302470141603` at `[0.010749, 0.454377, 0.244542, 0.172349, 0.372556, 0.739212]`
- Function 8: `9.6523968566016` at `[0.060159, 0.067445, 0.041598, 0.050182, 0.420959, 0.784634, 0.466707, 0.901447]`

Performance relative to the initial seed was strongest for Functions 1, 4, 5, 7, and 8. Function 2 improved only slightly over the seed best and then plateaued. Functions 3 and 6 improved, but remained harder to optimise consistently.

I measured success by:

- the best observed output value;
- improvement over the initial seed dataset;
- whether later rounds converged toward a stable high-performing region.

## Limitations

- The workflow is function-specific rather than fully standardized, so results depend on notebook-level choices.
- The evaluation budget is very small relative to the size of the search spaces.
- Gaussian Processes become less reliable as dimensionality increases.
- The black-box functions are synthetic and opaque, so there is no ground-truth explanation for why a region is optimal.
- The repository logs inputs and outputs, but not every acquisition setting or decision rationale for each round.
- Some results are sensitive to heuristics such as candidate radius, random seed, and exploration weight.

## Trade-Offs

- **Exploration vs exploitation:** Early rounds required broader exploration, but later rounds benefited much more from local exploitation around the best points.
- **Consistency vs flexibility:** A standardized pipeline would improve reproducibility, but adapting each notebook helped recover better results on difficult functions.
- **Global search vs trust-region search:** Global search was useful when little was known, but it became inefficient once strong local regions were identified.
- **Simplicity vs sophistication:** A transparent GP-based workflow was easier to debug and improve than a more complex optimisation stack, even if it was not the most automated approach possible.
