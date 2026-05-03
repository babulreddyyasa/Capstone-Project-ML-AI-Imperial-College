# Black-Box Optimisation Capstone Project

## Non-Technical Explanation Of Your Project
This project explores how to make good decisions when we can test a system but cannot see the formula behind it. I worked with eight hidden functions and had to choose one new query point for each function every week, with the goal of finding the highest possible output using only a small budget of evaluations. In simple terms, the project is about learning where to look next when each experiment is expensive and the landscape is unknown. My approach used past results to guide future choices, gradually moving from broad exploration to more focused search around the strongest regions.

## Data
The data comes from the BBO capstone setup itself rather than a public benchmark downloaded from an external source. Each function folder contains a course-provided initial seed dataset in `initial_inputs.npy` and `initial_outputs.npy`, and I then extended that data through my own weekly submissions stored in `inputs.txt` and `outputs.txt`.

The project covers eight functions with dimensionalities from 2D to 8D:

- Function 1: 10 seed points, 2 dimensions
- Function 2: 10 seed points, 2 dimensions
- Function 3: 15 seed points, 3 dimensions
- Function 4: 30 seed points, 4 dimensions
- Function 5: 20 seed points, 4 dimensions
- Function 6: 20 seed points, 5 dimensions
- Function 7: 30 seed points, 6 dimensions
- Function 8: 40 seed points, 8 dimensions

By the final stage of the project, I had added 13 weekly query rounds across all eight functions. Inputs are continuous vectors, usually in `[0, 1]`, and outputs are scalar objective values to maximize.

Project repository:
- https://github.com/babulreddyyasa/Capstone-Project-ML-AI-Imperial-College

## Model
I treated the project as a sequential optimisation policy rather than a single predictive model. The main modelling tool was Gaussian Process regression, implemented with `scikit-learn`, because it works well when data is limited and uncertainty matters. I paired the surrogate model with acquisition rules such as Upper Confidence Bound and Expected Improvement, then generated candidate points using a mix of global random search and local perturbation around the best observed region.

I chose this setup because it is transparent, flexible, and well suited to small-data black-box optimisation. It also allowed me to adapt the search behaviour by function. Lower-dimensional functions supported broader exploration and visual inspection, while higher-dimensional functions required tighter local trust-region search.

## Hyperparameter Optimisation
The main hyperparameters I tuned were not only model parameters but also search-policy settings:

- kernel choice, mainly RBF and Matérn kernels
- GP length scales and noise assumptions
- the UCB exploration weight `kappa`
- the number of candidate points sampled per round
- the radius of local candidate perturbations
- the balance between global exploration and local exploitation

I did not run a separate nested hyperparameter search. Instead, I tuned these settings iteratively based on how each function behaved. Early rounds used broader candidate clouds and stronger exploration. Later rounds used smaller trust regions, lower `kappa`, fixed random seeds, and more targeted candidate generation around the top-performing points.

## Results
The strongest overall lesson from the project was that the optimisation strategy improved as it became more disciplined and more function-specific. Some functions responded extremely well to late-stage local exploitation.

Best observed outputs after 13 submitted rounds:

- Function 1: `0.019275867963356643`
- Function 2: `0.6219564059776775`
- Function 3: `-0.0010417641626487369`
- Function 4: `-1.4677350476611966`
- Function 5: `3651.0406835289377`
- Function 6: `-0.5707448070355233`
- Function 7: `1.470302470141603`
- Function 8: `9.6523968566016`

The biggest gains came from tightening local search in the final weeks and correcting notebooks that were unintentionally drifting toward poor regions. Function 4 improved dramatically after I fixed the optimisation direction and moved the search back into a stronger area. Functions 1, 5, 7, and 8 also showed clear late-stage gains.

You can include images of plots using the code below:
![Screenshot](image.png)

## Optional: Contact Details
If you would like to discuss the project or reuse the workflow, the best starting point is the public repository:

- https://github.com/babulreddyyasa/Capstone-Project-ML-AI-Imperial-College
