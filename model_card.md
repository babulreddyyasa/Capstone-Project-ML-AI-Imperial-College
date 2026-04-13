# Model Card for the BBO Optimisation Approach

## 1. Overview

**Model name:** BBO Capstone Optimisation Approach  
**Type:** Sequential black-box optimisation workflow using Bayesian optimisation, Gaussian Process surrogates, acquisition functions, and local search heuristics  
**Version:** v1.0 repository model card, based on the 10 recorded query rounds in this project

This approach is not a single trained predictive model in the standard supervised-learning sense. Instead, it is an optimisation policy: a method for deciding which point to query next when the objective function is unknown, expensive to evaluate, and only observable through input-output queries.

Across the project, the approach combines:

- Gaussian Process (GP) surrogate modelling;
- acquisition-guided search using Upper Confidence Bound (UCB) and Expected Improvement (EI);
- random/global exploration;
- local perturbation around the current best point;
- function-specific heuristics for handling different dimensionalities and response shapes.

## 2. Intended Use

This optimisation approach is suitable for:

- educational black-box optimisation tasks;
- low- to moderate-dimensional continuous search spaces;
- settings with limited evaluation budgets;
- problems where the objective can be queried but gradients are unavailable;
- exploratory optimisation problems such as source detection, synthetic benchmark tuning, or expensive parameter search.

Use cases that should be avoided include:

- safety-critical deployment without independent validation;
- physical contamination or radiation response systems where false confidence could create operational risk;
- problems with strict constraints that are not explicitly modelled;
- high-noise, high-dimensional, or strongly discontinuous objectives without additional robustness measures;
- claims of guaranteed global optimality.

## 3. Model Details and Decision Process

### Core decision logic

At each round, the approach uses previously observed query points and outputs to estimate which untested point is most promising. In practice, the decision process is:

1. fit a surrogate model, usually a Gaussian Process, to the currently available data;
2. estimate either expected gain or a reward-plus-uncertainty score over candidate points;
3. combine global exploration with local refinement near strong-performing observations;
4. submit the candidate predicted to be most useful under the acquisition rule.

This means the approach makes decisions by balancing two goals:

- **exploration**, to reduce uncertainty and discover new promising regions;
- **exploitation**, to refine areas that already appear strong.

### Strategy across the ten rounds

The ten logged rounds show an evolving optimisation workflow rather than a rigid single algorithm.

**Rounds 1-2:**  
The early rounds prioritized broad coverage and signal detection. Candidate points were spread across the domain to identify which functions had exploitable structure and which regions were non-trivial. This stage was especially important for sparse-response functions such as Function 1.

**Rounds 3-5:**  
Once promising regions appeared, the strategy shifted toward GP-guided search. UCB-style acquisition was used in some notebooks to score many candidate points, while local perturbations around the current best observation were introduced to sharpen the search.

**Rounds 6-8:**  
The approach became more function-specific. Lower-dimensional functions received tighter local search around discovered peaks. Higher-dimensional functions relied more heavily on surrogate predictions and heuristic candidate generation because direct visualization and exhaustive probing were less practical.

**Rounds 9-10:**  
The final rounds emphasized exploitation of the strongest regions found so far, with continued limited global exploration to avoid complete premature convergence. This worked well on some functions, especially Functions 1, 5, and 8, but it also exposed instability on functions where the search may have over-committed to misleading local structure, particularly Functions 4, 6, and 7.

### Techniques used

The notebooks and project write-up indicate the following techniques were used during the optimisation process:

- Gaussian Process regression with kernels such as RBF and Matérn;
- Upper Confidence Bound for balancing mean prediction and uncertainty;
- Expected Improvement in at least some later or function-specific notebooks;
- random candidate generation over the full domain;
- local candidate generation by small perturbations around the current best point;
- visualization and heuristic inspection to guide search refinement;
- function-specific adaptation rather than enforcing exactly one acquisition rule for all tasks.

This flexibility is one of the approach’s strengths, but it also reduces standardization across functions.

## 4. Performance

### Evaluation metric

The primary metric was the **best observed objective value** for each function under a maximization framing. Secondary evidence of performance came from:

- improvement over the initial seed set;
- best result achieved during the 10 recorded rounds;
- qualitative stability of search behavior across rounds.

No single aggregate benchmark score, regret curve, or confidence interval is stored in the repository, so the strongest available performance summary is based on observed best values.

### Results across the eight functions

Best values observed in the 10 logged rounds:

- Function 1: `0.005716883885302281` at round 10, improving substantially over the initial best `7.710875114502849e-16`
- Function 2: `0.6219564059776775` at round 2, slightly above the initial best `0.6112052157608581`
- Function 3: `-0.009153765203187628` at round 8, improving over the initial best `-0.034835313350111966`
- Function 4: `-28.914094514315888` at round 1, below the initial best `-4.02554228191276`
- Function 5: `2777.805513886249` at round 10, far above the initial best `1088.8596182017116`
- Function 6: `-0.5707448070355233` at round 9, modestly above the initial best `-0.7142649478203608`
- Function 7: `1.0564175188888865` at round 6, below the initial best `1.3649683044957453`
- Function 8: `9.605274086169` at round 10, slightly above the initial best `9.598482002566237`

### Performance interpretation

The approach performed best when:

- the search space was structured enough for the surrogate to identify promising regions;
- local refinement could exploit a meaningful signal once found;
- the function rewarded concentrated search near peaks.

It performed less well when:

- the initial seed set already contained very strong points and later rounds failed to improve on them;
- the optimisation policy may have over-exploited weak local structure;
- function geometry, scaling, or acquisition tuning were not well matched.

Overall, the recorded results suggest:

- **strong gains** on Functions 1 and 5;
- **small but positive gains** on Functions 2, 3, 6, and 8;
- **underperformance relative to the initial seed** on Functions 4 and 7.

## 5. Assumptions and Limitations

This optimisation approach relies on several assumptions:

- the objective is informative enough that past observations help predict better future queries;
- nearby points often contain useful local structure;
- a GP surrogate is a reasonable approximation of the underlying response surface;
- the function is queried over a bounded continuous domain;
- a limited number of evaluations can still reveal a useful signal.

Key limitations and failure modes:

- sensitivity to kernel choice, acquisition settings, and candidate-generation heuristics;
- risk of premature convergence to local optima;
- reduced reliability in higher dimensions or poorly scaled spaces;
- difficulty handling objectives with abrupt discontinuities or heavy noise;
- inconsistent behavior across functions because the strategy was adapted rather than fully standardized;
- lack of explicit uncertainty calibration reporting, regret tracking, or robust ablation studies.

Another practical limitation is that the repository logs outputs and inputs, but not full decision metadata for every round. That makes retrospective diagnosis harder when a function’s later rounds regress.

## 6. Ethical Considerations

Transparency is important here because the approach could easily look more reliable than it is if only the successful cases were highlighted. A clear model card supports reproducibility and responsible interpretation by documenting:

- what the optimisation policy is actually doing;
- where it improved performance and where it did not;
- what assumptions underlie its recommendations;
- why its suggestions should not be treated as ground truth in real-world high-stakes settings.

For real-world adaptation, transparency matters in two ways. First, it helps other practitioners reproduce the workflow, inspect acquisition choices, and understand why a particular query was proposed. Second, it makes it easier to decide what extra safeguards would be needed before using a similar method in applied domains such as environmental monitoring or experimental design.

## 7. Strengths, Limitations, and Sufficiency of the Card

### Strengths

- adapts to multiple black-box functions with different dimensions;
- combines principled surrogate modeling with practical heuristics;
- supports query-efficient optimisation under limited budgets;
- produces interpretable next-query suggestions rather than opaque end-to-end outputs.

### Limitations

- performance is uneven across functions;
- the approach depends heavily on tuning and notebook-level choices;
- later-stage exploitation can become brittle if the surrogate is misled;
- reproducibility is limited by incomplete logging of per-round rationale and settings.

### Is more detail needed?

More detail could improve the model card if this project were being prepared for external reuse, benchmarking, or operational deployment. In that case, it would help to add:

- per-function acquisition settings;
- kernel hyperparameters by round;
- regret curves or improvement trajectories;
- ablation results comparing UCB, EI, and random search;
- explicit versioning for notebook changes.

For the current capstone purpose, the present structure is sufficient because it explains:

- how the optimisation approach makes decisions;
- where it worked and where it struggled;
- what assumptions and limitations frame the results;
- why transparent documentation matters for reproducibility.

That level of detail is enough to make the approach understandable, critically assessable, and aligned with a model-card format without overloading the reader with implementation minutiae that are better kept in notebooks or appendices.
