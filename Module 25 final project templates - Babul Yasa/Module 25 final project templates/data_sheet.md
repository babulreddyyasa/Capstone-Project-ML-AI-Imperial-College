# Datasheet

This datasheet documents the dataset and optimisation trace used in my black-box optimisation capstone project. The project covers eight hidden objective functions, so some answers summarize the project as a whole and then note important function-specific differences.

## Function Overview

1. Which function does this datasheet describe?  
This datasheet covers the full BBO capstone project across Functions 1 to 8 rather than only one function.

2. What real-world scenario does this function simulate?  
The project uses synthetic black-box functions that stand in for expensive real-world search problems. Function 1 explicitly resembles contamination or radiation-source detection in a 2D area, where most points give near-zero signal unless the query is close to a source. The higher-dimensional functions are best interpreted as generic experimental design or parameter-tuning tasks where the objective is only revealed after an evaluation.

3. What is the dimensionality of the input?  
The dimensionality ranges from 2D to 8D:

- Function 1: 2D
- Function 2: 2D
- Function 3: 3D
- Function 4: 4D
- Function 5: 4D
- Function 6: 5D
- Function 7: 6D
- Function 8: 8D

4. How many initial data points were provided?  
The course-provided seed data contained:

- Function 1: 10 points
- Function 2: 10 points
- Function 3: 15 points
- Function 4: 30 points
- Function 5: 20 points
- Function 6: 20 points
- Function 7: 30 points
- Function 8: 40 points

5. What does the output represent?  
The output is a scalar objective score to be maximized. Depending on the function, it can be interpreted as signal strength, quality, performance, or utility. Some values are negative, but they are still treated as maximization targets where less negative is better.

## Nature Of The Data

1. Describe the structure of the initial dataset.  
Each function has an `initial_inputs.npy` array and an `initial_outputs.npy` array. The input arrays have shape `(n, d)` and the outputs have shape `(n,)`, where `n` is the number of seed points and `d` is the number of dimensions for that function.

2. How does the dataset evolve as you add new queries weekly?  
Each week, I submitted one additional point for every function. Those new points were appended to `inputs.txt`, and the returned outputs were appended to `outputs.txt`. By the end of the project, the trace had grown by 13 rounds, which made the data progressively less exploratory and more focused on the strongest regions.

3. Does the function include noise or randomness?  
The functions appear mostly deterministic from the outside. I did not observe evidence that the same input would return different outputs, although I did not repeatedly query identical points as a formal noise test. Any uncertainty in the project mainly came from limited data and model approximation rather than obvious stochastic outputs.

4. Based on observations, does the function appear unimodal, multimodal, noisy, or smooth?  
The landscape varies by function. Function 1 behaves like a sparse multimodal detection problem. Functions 5 and 8 appear smoother and reward steady local refinement. Functions 4 and 6 were harder to model and more sensitive to incorrect search direction or local trapping. Overall, the project includes a mix of sparse, smooth, boundary-sensitive, and locally deceptive landscapes.

## Your Optimisation Strategy

1. Which optimisation method(s) did you use?  
I used Bayesian optimisation with Gaussian Process surrogates, Upper Confidence Bound, Expected Improvement, random candidate generation, local trust-region search, and manual reasoning based on observed trends.

2. Why did you choose this method for this particular function?  
The budget was small, gradients were unavailable, and the functions were opaque. Gaussian Processes were a practical choice because they work well in low- to moderate-data settings and provide uncertainty estimates that help balance exploration and exploitation.

3. How did you balance exploration and exploitation?  
In early rounds I used broader candidate pools and higher exploration pressure. In later rounds I reduced `kappa`, shrank local search radii, and concentrated more samples around the top few points. That let me exploit proven high-value regions without removing exploration entirely.

4. Did your strategy change over the weeks? Why?  
Yes. The strategy became more structured and more local over time. I also corrected notebooks that were implicitly behaving like minimization routines, added fixed random seeds for reproducibility, and printed better debugging information so that stale notebook outputs were easier to detect.

## Data Handling And Preprocessing

1. Did you rescale or normalise inputs? Why or why not?  
I generally did not rescale the inputs because they were already expressed on a common bounded range close to `[0, 1]`.

2. Did you train any surrogate models?  
Yes. The main surrogate model was Gaussian Process regression.

3. If yes, what preprocessing did the surrogate require?  
The main preparation steps were concatenating the seed data with the logged query history, flattening output arrays when needed, clipping candidate points to the valid input range, and using `normalize_y=True` in the GP fit. I also tuned kernels and local search radius by function.

4. Did you handle outliers or unusual data points?  
I did not remove outliers. In this project, unusual points were informative because they often revealed bad regions, boundary effects, or misleading local structure. Instead of filtering them, I used them to adjust the search policy.

## Weekly Iteration And Learning

1. How did new data points change your understanding of the function landscape?  
New points gradually revealed which functions had broad ridges and which had tight local peaks. They also showed that late-stage optimisation benefited more from disciplined local refinement than from large global jumps.

2. Did you encounter local optima? How did you detect them?  
Yes. I detected them when a region looked promising for a few rounds but additional nearby points stopped improving or when the model kept suggesting boundary-heavy exploration that produced weaker outputs.

3. Which queried inputs were most informative and why?  
The most informative points were the ones that either sharply improved the best value or corrected an incorrect search direction. Examples include the late Function 1 points near `[0.6507, 0.663]`, the Function 4 recovery around `[0.485150, 0.460770, 0.413452, 0.339324]`, and the final Function 5 and Function 8 refinements near their boundary-adjacent optima.

4. If you restarted, what would you do differently?  
I would standardize the notebooks earlier, log the acquisition settings for each round, compare UCB and EI more systematically, and use a cleaner convergence dashboard instead of relying on notebook-by-notebook inspection.

## Performance And Results

1. What is the best output value you achieved?  
Best values by function were:

- Function 1: `0.019275867963356643`
- Function 2: `0.6219564059776775`
- Function 3: `-0.0010417641626487369`
- Function 4: `-1.4677350476611966`
- Function 5: `3651.0406835289377`
- Function 6: `-0.5707448070355233`
- Function 7: `1.470302470141603`
- Function 8: `9.6523968566016`

2. Which input vector produced this value?  

- Function 1: `[0.650720, 0.662477]`
- Function 2: `[0.680214, 0.502871]`
- Function 3: `[0.355806, 0.576302, 0.482483]`
- Function 4: `[0.485150, 0.460770, 0.413452, 0.339324]`
- Function 5: `[0.205624, 0.969307, 0.967528, 1.000000]`
- Function 6: `[0.884317, 0.312451, 0.668732, 0.699154, 0.156208]`
- Function 7: `[0.010749, 0.454377, 0.244542, 0.172349, 0.372556, 0.739212]`
- Function 8: `[0.060159, 0.067445, 0.041598, 0.050182, 0.420959, 0.784634, 0.466707, 0.901447]`

3. How confident are you that this is near the global maximum? Why?  
My confidence is moderate, not absolute. I am more confident for Functions 1, 5, 7, and 8 because later rounds kept improving within a stable local region. I am less confident for Functions 2, 3, and 6 because those functions either plateaued early or remained sensitive to small changes. The budget is too limited to claim global optimality.

4. Did your results align with expectations for this function?  
Mostly yes. Sparse or structured problems responded well once a useful region was found. The most surprising result was how much performance improved after fixing search-direction mistakes and tightening local search in the later rounds.

## Ethical, Practical And General Considerations

1. How does this black-box optimisation task relate to real-world applications?  
It is similar to real tasks such as environmental hotspot detection, expensive laboratory experiments, industrial parameter tuning, and ML hyperparameter optimisation, where each evaluation has a cost and the full objective is unknown.

2. What limitations arise from the synthetic nature of the function?  
Synthetic functions hide many real-world complications such as delayed feedback, changing conditions, hard constraints, safety limits, and measurement error. That means good capstone performance does not automatically transfer to operational settings.

3. Would your strategy scale to more serious or more expensive problems? Why or why not?  
Partly. The general idea of surrogate-assisted search would scale, but a more serious setting would need better uncertainty calibration, constraint handling, experiment tracking, and stronger governance around when it is safe to exploit a predicted optimum.

4. What risks or pitfalls should a future user be aware of when analysing this function?  
The main risks are over-trusting a small surrogate model, over-exploiting a misleading local region, and misreading notebook outputs if runs are stale or out of order. A future user should also be careful not to assume that all functions respond well to the same acquisition settings.
