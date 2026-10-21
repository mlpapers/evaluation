# Model Evaluation and Comparison

## Evaluation targets

- **Fitted model versus learning procedure:** performance of one trained model differs from average performance over new training sets. [Bates et al.](https://arxiv.org/abs/2104.00673) (2021) establish this distinction for cross-validation in ordinary least squares.
- **Selection versus assessment:** optimizing a noisy validation criterion can overfit it; evaluating the selected winner on that same criterion is optimistic. [Cawley and Talbot](https://jmlr.org/papers/v11/cawley10a.html) (2010) find selection effects comparable to algorithm differences.

## Splitting and resampling

- **Cross-validation:** holdout, k-fold and leave-one-out trade training-set size, computation and dependence among error estimates. [Arlot and Celisse](https://arxiv.org/abs/0907.4728) (2009/2010) distinguish risk estimation from selecting a model; the best procedure depends on the objective.
- **Nested cross-validation for selection:** tune preprocessing, features and hyperparameters inside each outer training fold; assess the resulting procedure on its outer test fold. [Cawley and Talbot](https://jmlr.org/papers/v11/cawley10a.html) (2010). Outer results must not drive further selection.
- **Group-aware validation:** [GroupKFold and leave-one-group-out](https://scikit-learn.org/stable/modules/cross_validation.html) prevent the same subject, device or group from appearing on both sides of a split. Group choice defines whether evaluation tests new observations or generalization to new groups.
- **Rolling-origin evaluation:** repeatedly train on earlier observations and test later horizons. [Forecasting: Principles and Practice](https://otexts.com/fpp3/tscv.html) (2021). Match forecast horizons to deployment; random folds can expose information unavailable at prediction time.
- **Stratification:** preserves approximate class proportions across folds. [Cross-validation documentation](https://scikit-learn.org/stable/modules/cross_validation.html) warns that this engineering safeguard can shrink observed fold variability; it does not remove sampling uncertainty.
- **Leakage-safe preprocessing:** fit imputation, scaling, selection and learned representations within training splits. [Pipelines](https://scikit-learn.org/stable/common_pitfalls.html) help enforce this; preprocessing or selecting a pipeline using test outcomes still leaks information.
- **.632+ bootstrap:** combines training and out-of-bag error with a weight adjusted for overfitting. [Efron and Tibshirani](https://sites.stat.washington.edu/courses/stat527/s13/readings/EfronTibshirani_JASA_1997.pdf) (1997) study classification error estimation; this is an error estimator, not a confidence interval.
- **[Data thinning](https://jmlr.org/papers/v25/23-0446.html)** - 2024. Under convolution-closed models, splits each observation into independent parts for clustering/PCA validation without holding out rows. Gaussian thinning needs the correct variance/covariance; misspecification can induce dependence.

## Uncertainty in reported performance

- **Dependent folds:** [Bengio and Grandvalet](https://jmlr.org/papers/v5/grandvalet04a.html) (2004) prove no universally unbiased estimator of k-fold CV variance exists. Ignoring error correlations can underestimate uncertainty; extra folds are not independent replications.
- **Nested CV for uncertainty:** [Bates et al.](https://arxiv.org/abs/2104.00673) (2021) estimate prediction-error variance using nested resampling; reported interval coverage improves where naive CV intervals fail. This objective differs from nesting for hyperparameter selection.
- **Small-sample evaluation:** [Varoquaux](https://arxiv.org/abs/1706.07581) (2017/2018) shows wide prediction-accuracy uncertainty in small neuroimaging samples. Standard errors across folds can understate it; resampling cannot substitute for collecting independent observations.
- **Training and search variability:** [Bouthillier et al.](https://arxiv.org/abs/2103.03098) (2021) find data sampling, initialization and hyperparameter choices materially affect comparisons on five tasks. Repeating one fitted model’s test evaluation misses these sources.
- **Stratified bootstrap intervals:** resample runs within benchmark tasks to quantify uncertainty in aggregate scores. [Agarwal et al.](https://arxiv.org/abs/2108.13264) (2021). Specify whether tasks themselves are fixed or sampled; the resampling unit determines the uncertainty represented.

## Metrics and scoring rules

- **Thresholded classification:** precision, recall and F-scores evaluate decisions at a chosen threshold; macro and pooled averaging weight classes differently. [Metric definitions](https://scikit-learn.org/stable/modules/model_evaluation.html). Report the threshold-selection procedure.
- **ROC versus precision–recall:** [Davis and Goadrich](https://ftp.cs.wisc.edu/machine-learning/shavlik-group/davis.icml06.pdf) (ICML 2006) relate both curve spaces. ROC-AUC optimization need not optimize PR-AUC; precision depends on class prevalence, so comparisons need the class mix.
- **Point-prediction losses:** squared error targets the conditional mean; absolute error targets a conditional median. [Regression metrics](https://scikit-learn.org/stable/modules/model_evaluation.html). Choosing a loss specifies the prediction being evaluated, not only its units.
- **Proper scoring rules:** Brier and logarithmic scores assess predictive probabilities; CRPS assesses predictive distributions. [Gneiting and Raftery](https://sites.stat.washington.edu/people/raftery/Research/PDF/Gneiting2007jasa.pdf) (2007). Propriety rewards truthful distributions in expectation.
- **Interval scores:** combine interval width with penalties for observations outside the interval. [Gneiting and Raftery](https://sites.stat.washington.edu/people/raftery/Research/PDF/Gneiting2007jasa.pdf) (2007). Coverage alone rewards arbitrarily wide intervals and misses useful sharpness.
- **Calibration-error estimation:** binning, sample size and estimator bias affect reported calibration. [Kumar et al.](https://arxiv.org/abs/1909.10155) (NeurIPS 2019) develop more accurate estimators and scaling–binning calibration; smooth reliability plots alone are insufficient evidence.

## Prediction-set coverage

- **[Split conformal prediction](https://arxiv.org/abs/2107.07511)** — 2021 overview. Calibrates a fitted predictor’s scores on held-out data to form prediction sets. Exchangeability supports finite-sample marginal coverage; report set size and subgroup coverage; the guarantee is not conditional on every input.
- **[Conformal prediction under covariate shift](https://arxiv.org/abs/1904.06019)** — 2019. Weights calibration scores by target-to-training covariate density ratios while holding the conditional response law fixed. Exact known-weight guarantees do not automatically carry over to inaccurately estimated ratios.

## Statistical comparisons

- **Permutation tests:** shuffle labels to test class structure, or shuffle features within classes to test exploited dependencies. [Ojala and Garriga](https://jmlr.org/papers/v11/ojala10a.html) (2010). The permitted shuffles define the tested null hypothesis.
- **Comparisons across datasets:** Wilcoxon signed-rank compares two methods; Friedman with post-hoc tests compares several. [Demšar](https://jmlr.org/papers/v7/demsar06a.html) (2006). Datasets are the comparison units; correlated CV folds are not interchangeable with independent datasets.
- **Bayesian comparisons:** estimate probabilities of superiority, practical equivalence and inferiority under a statistical model. [Benavoli et al.](https://jmlr.org/papers/v18/16-305.html) (2017). Specify the equivalence region and dependence model; these differ from null-hypothesis p-values.

## Group fairness criteria

- **[Equalized odds and opportunity](https://arxiv.org/abs/1610.02413)** — 2016. Equalized odds matches error rates across groups conditional on the outcome; equal opportunity requires equal true-positive rates. Hardt et al. construct post-processing rules; the criteria use observed labels and group membership.
- **[Conflicting fairness criteria](https://arxiv.org/abs/1610.07524)** — 2016/2017. Chouldechova shows that predictive-value parity and equal false-positive/negative rates conflict at unequal base rates for nondegenerate imperfect classifiers. A fairness target must specify which errors and populations matter.

## Benchmarks, reuse and distribution shift

- **Robust benchmark summaries:** interquartile mean and performance profiles expose information hidden by one mean or median. [Agarwal et al.](https://arxiv.org/abs/2108.13264) (NeurIPS 2021) show uncertainty-aware analyses change conclusions on studied few-run RL benchmarks.
- **Adaptive holdout reuse:** repeated feedback can overfit a test set. [Dwork et al.](https://arxiv.org/abs/1506.02629) (2015) use controlled, randomized access to limit this effect; guarantees belong to the access procedure, not unrestricted repeated leaderboard inspection.
- **Learning curves:** compare held-out performance against training-set size. [Viering and Loog](https://arxiv.org/abs/2103.10948) (2021) document diverse shapes, including nonmonotonic curves; extrapolating a fitted power law is an additional modeling assumption.
- **[LLM-as-judge evaluation](https://arxiv.org/abs/2306.05685)** — 2023. MT-Bench studies agreement with human preferences alongside position, verbosity and self-preference biases. A model judge approximates judgments in a tested setting; it is not a factual oracle or a universal replacement for human assessment.
- **Natural distribution shifts:** [WILDS](https://arxiv.org/abs/2012.07421) (2020/ICML 2021) benchmarks hospital, location, time and other shifts. Standard training and tested shift-handling methods retain substantial out-of-distribution gaps; the split defines the generalization claim.
- **Uncertainty under shift:** [Ovadia et al.](https://arxiv.org/abs/1906.02530) (NeurIPS 2019) find in-distribution post-hoc calibration insufficient under tested shifts; some model-averaging methods fare better. Evaluate predictive accuracy and calibration separately.

## Software and references

- **scikit-learn:** [splitters](https://scikit-learn.org/stable/modules/cross_validation.html), [scoring functions](https://scikit-learn.org/stable/modules/model_evaluation.html) and [pipelines](https://scikit-learn.org/stable/common_pitfalls.html) support evaluation workflows. The chosen split, search and scorer still determine what is estimated.
- **rliable:** [implementation](https://github.com/google-research/rliable) of interval estimates, interquartile means, probability of improvement and performance profiles for benchmark score arrays. Run/task structure and score normalization remain explicit inputs to the analysis.
- **baycomp:** [Bayesian comparison tools](https://baycomp.readthedocs.io/en/latest/) for classifiers on one or multiple datasets; returns probabilities for superiority, practical equivalence and inferiority. The caller supplies the equivalence threshold.

## Related topics

[AutoML](https://mlpapers.org/automl/) · [Feature selection](https://mlpapers.org/feature-selection/) · [Anomaly detection](https://mlpapers.org/anomaly-detection/) · [Bayesian inference](https://mlpapers.org/bayesian-inference/) · [Interpretability](https://mlpapers.org/interpretability/) · [Transfer learning](https://mlpapers.org/transfer-learning/).
