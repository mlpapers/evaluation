# Model Evaluation and Comparison

## Evaluation targets

- **Fitted model versus learning procedure** - Performance of one trained model differs from average performance over new training sets. Bates et al. establish this distinction for cross-validation in ordinary least squares. [Bates et al. 2023](<https://arxiv.org/abs/2104.00673> "Cross-validation: what does it estimate and how well does it do it?; JASA").
- **Selection versus assessment** - Optimizing a noisy validation criterion can overfit it; evaluating the selected winner on that same criterion is optimistic. Cawley and Talbot find selection effects comparable to algorithm differences. [Cawley and Talbot 2010](<https://jmlr.org/papers/v11/cawley10a.html> "On Over-fitting in Model Selection and Subsequent Selection Bias in Performance Evaluation; JMLR").

## Splitting and resampling

- **Cross-validation** - Holdout, k-fold and leave-one-out trade training-set size, computation and dependence among error estimates. Arlot and Celisse distinguish risk estimation from selecting a model; the best procedure depends on the objective. [Arlot and Celisse 2010](<https://arxiv.org/abs/0907.4728> "A survey of cross-validation procedures for model selection; Statist. Surv.").
- **Nested cross-validation for selection** - Tune preprocessing, features and hyperparameters inside each outer training fold; assess the resulting procedure on its outer test fold. Outer results must not drive further selection. [Cawley and Talbot 2010](<https://jmlr.org/papers/v11/cawley10a.html> "On Over-fitting in Model Selection and Subsequent Selection Bias in Performance Evaluation; JMLR").
- **Group-aware validation** - GroupKFold and leave-one-group-out prevent the same subject, device or group from appearing on both sides of a split. Group choice defines whether evaluation tests new observations or generalization to new groups. [docs](<https://scikit-learn.org/stable/modules/cross_validation.html>).
- **Rolling-origin evaluation** - Repeatedly train on earlier observations and test later horizons. Match forecast horizons to deployment; random folds can expose information unavailable at prediction time. [Tashman 2000](<https://doi.org/10.1016/S0169-2070(00)00065-0> "Out-of-sample tests of forecasting accuracy: an analysis and review; IJF"), [Hyndman and Athanasopoulos 2021](<https://otexts.com/fpp3/tscv.html> "Forecasting: Principles and Practice; book").
- **Stratification** - Preserves approximate class proportions across folds. Cross-validation documentation warns that this engineering safeguard can shrink observed fold variability; it does not remove sampling uncertainty. [docs](<https://scikit-learn.org/stable/modules/cross_validation.html>).
- **Leakage-safe preprocessing** - Fit imputation, scaling, selection and learned representations within training splits. Pipelines help enforce this; preprocessing or selecting a pipeline using test outcomes still leaks information. [docs](<https://scikit-learn.org/stable/common_pitfalls.html>).
- **.632+ bootstrap** - Combines training and out-of-bag error with a weight adjusted for overfitting. Efron and Tibshirani study classification error estimation; this is an error estimator, not a confidence interval. [Efron and Tibshirani 1997](<https://sites.stat.washington.edu/courses/stat527/s13/readings/EfronTibshirani_JASA_1997.pdf> "Improvements on Cross-Validation: The 632+ Bootstrap Method; JASA").
- **Data thinning** - Under convolution-closed models, splits each observation into independent parts for clustering/PCA validation without holding out rows. Gaussian thinning needs the correct variance/covariance; misspecification can induce dependence. [Neufeld et al. 2024](<https://jmlr.org/papers/v25/23-0446.html> "Data Thinning for Convolution-Closed Distributions; JMLR").

## Uncertainty in reported performance

- **Dependent folds** - Bengio and Grandvalet prove no universally unbiased estimator of k-fold CV variance exists. Ignoring error correlations can underestimate uncertainty; extra folds are not independent replications. [Bengio and Grandvalet 2004](<https://jmlr.org/papers/v5/grandvalet04a.html> "No Unbiased Estimator of the Variance of K-Fold Cross-Validation; JMLR").
- **Nested CV for uncertainty** - Bates et al. estimate prediction-error variance using nested resampling; reported interval coverage improves where naive CV intervals fail. This objective differs from nesting for hyperparameter selection. [Bates et al. 2023](<https://arxiv.org/abs/2104.00673> "Cross-validation: what does it estimate and how well does it do it?; JASA").
- **Small-sample evaluation** - Varoquaux shows wide prediction-accuracy uncertainty in small neuroimaging samples. Standard errors across folds can understate it; resampling cannot substitute for collecting independent observations. [Varoquaux 2017](<https://arxiv.org/abs/1706.07581> "Cross-validation failure: small sample sizes lead to large error bars; arXiv").
- **Training and search variability** - Bouthillier et al. find data sampling, initialization and hyperparameter choices materially affect comparisons on five tasks. Repeating one fitted model's test evaluation misses these sources. [Bouthillier et al. 2021](<https://arxiv.org/abs/2103.03098> "Accounting for Variance in Machine Learning Benchmarks; arXiv").
- **Stratified bootstrap intervals** - Resample runs within benchmark tasks to quantify uncertainty in aggregate scores. Specify whether tasks themselves are fixed or sampled; the resampling unit determines the uncertainty represented. [Agarwal et al. 2021](<https://arxiv.org/abs/2108.13264> "Deep Reinforcement Learning at the Edge of the Statistical Precipice; NeurIPS").

## Metrics and scoring rules

- **Thresholded classification** - Precision, recall and F-scores evaluate decisions at a chosen threshold; macro and pooled averaging weight classes differently. Report the threshold-selection procedure. [docs](<https://scikit-learn.org/stable/modules/model_evaluation.html>).
- **ROC versus precision-recall** - Davis and Goadrich relate both curve spaces. ROC-AUC optimization need not optimize PR-AUC; precision depends on class prevalence, so comparisons need the class mix. [Davis and Goadrich 2006](<https://ftp.cs.wisc.edu/machine-learning/shavlik-group/davis.icml06.pdf> "The relationship between Precision-Recall and ROC curves; ICML").
- **Point-prediction losses** - Squared error targets the conditional mean; absolute error targets a conditional median. Choosing a loss specifies the prediction being evaluated, not only its units. [docs](<https://scikit-learn.org/stable/modules/model_evaluation.html>).
- **Proper scoring rules** - Brier and logarithmic scores assess predictive probabilities; CRPS assesses predictive distributions. Propriety rewards truthful distributions in expectation. [Gneiting and Raftery 2007](<https://sites.stat.washington.edu/people/raftery/Research/PDF/Gneiting2007jasa.pdf> "Strictly Proper Scoring Rules, Prediction, and Estimation; JASA").
- **Interval scores** - Combine interval width with penalties for observations outside the interval. Coverage alone rewards arbitrarily wide intervals and misses useful sharpness. [Gneiting and Raftery 2007](<https://sites.stat.washington.edu/people/raftery/Research/PDF/Gneiting2007jasa.pdf> "Strictly Proper Scoring Rules, Prediction, and Estimation; JASA").
- **Calibration-error estimation** - Binning, sample size and estimator bias affect reported calibration. Kumar et al. develop more accurate estimators and scaling-binning calibration; smooth reliability plots alone are insufficient evidence. [Kumar et al. 2019](<https://arxiv.org/abs/1909.10155> "Verified Uncertainty Calibration; NeurIPS").

## Prediction-set coverage

- **Split conformal prediction** - Calibrates a fitted predictor's scores on held-out data to form prediction sets. Exchangeability supports finite-sample marginal coverage; report set size and subgroup coverage; the guarantee is not conditional on every input. [Angelopoulos and Bates 2021](<https://arxiv.org/abs/2107.07511> "A Gentle Introduction to Conformal Prediction and Distribution-Free Uncertainty Quantification; arXiv").
- **Conformal prediction under covariate shift** - Weights calibration scores by target-to-training covariate density ratios while holding the conditional response law fixed. Exact known-weight guarantees do not automatically carry over to inaccurately estimated ratios. [Tibshirani et al. 2019](<https://arxiv.org/abs/1904.06019> "Conformal Prediction Under Covariate Shift; NeurIPS").

## Statistical comparisons

- **Permutation tests** - Shuffle labels to test class structure, or shuffle features within classes to test exploited dependencies. The permitted shuffles define the tested null hypothesis. [Ojala and Garriga 2010](<https://jmlr.org/papers/v11/ojala10a.html> "Permutation Tests for Studying Classifier Performance; JMLR").
- **Comparisons across datasets** - Wilcoxon signed-rank compares two methods; Friedman with post-hoc tests compares several. Datasets are the comparison units; correlated CV folds are not interchangeable with independent datasets. [Demšar 2006](<https://jmlr.org/papers/v7/demsar06a.html> "Statistical Comparisons of Classifiers over Multiple Data Sets; JMLR").
- **Bayesian comparisons** - Estimate probabilities of superiority, practical equivalence and inferiority under a statistical model. Specify the equivalence region and dependence model; these differ from null-hypothesis p-values. [Benavoli et al. 2017](<https://jmlr.org/papers/v18/16-305.html> "Time for a Change: a Tutorial for Comparing Multiple Classifiers Through Bayesian Analysis; JMLR").

## Group fairness criteria

- **Equalized odds and opportunity** - Equalized odds matches error rates across groups conditional on the outcome; equal opportunity requires equal true-positive rates. Hardt et al. construct post-processing rules; the criteria use observed labels and group membership. [Hardt et al. 2016](<https://arxiv.org/abs/1610.02413> "Equality of Opportunity in Supervised Learning; NeurIPS").
- **Conflicting fairness criteria** - Chouldechova shows that predictive-value parity and equal false-positive/negative rates conflict at unequal base rates for nondegenerate imperfect classifiers. A fairness target must specify which errors and populations matter. [Chouldechova 2016](<https://arxiv.org/abs/1610.07524> "Fair prediction with disparate impact: A study of bias in recidivism prediction instruments; arXiv").

## Benchmarks, reuse and distribution shift

- **Robust benchmark summaries** - Interquartile mean and performance profiles expose information hidden by one mean or median. Agarwal et al. show uncertainty-aware analyses change conclusions on studied few-run RL benchmarks. [Agarwal et al. 2021](<https://arxiv.org/abs/2108.13264> "Deep Reinforcement Learning at the Edge of the Statistical Precipice; NeurIPS").
- **Adaptive holdout reuse** - Repeated feedback can overfit a test set. Dwork et al. use controlled, randomized access to limit this effect; guarantees belong to the access procedure, not unrestricted repeated leaderboard inspection. [Dwork et al. 2015](<https://arxiv.org/abs/1506.02629> "Generalization in Adaptive Data Analysis and Holdout Reuse; NeurIPS").
- **Learning curves** - Compare held-out performance against training-set size. Viering and Loog document diverse shapes, including nonmonotonic curves; extrapolating a fitted power law is an additional modeling assumption. [Viering and Loog 2021](<https://arxiv.org/abs/2103.10948> "The Shape of Learning Curves: a Review; arXiv").
- **LLM-as-judge evaluation** - MT-Bench studies agreement with human preferences alongside position, verbosity and self-preference biases. A model judge approximates judgments in a tested setting; it is not a factual oracle or a universal replacement for human assessment. [Zheng et al. 2023](<https://arxiv.org/abs/2306.05685> "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena; NeurIPS").
- **Natural distribution shifts** - WILDS benchmarks hospital, location, time and other shifts. Standard training and tested shift-handling methods retain substantial out-of-distribution gaps; the split defines the generalization claim. [Koh et al. 2021](<https://arxiv.org/abs/2012.07421> "WILDS: A Benchmark of in-the-Wild Distribution Shifts; ICML").
- **Uncertainty under shift** - Ovadia et al. find in-distribution post-hoc calibration insufficient under tested shifts; some model-averaging methods fare better. Evaluate predictive accuracy and calibration separately. [Ovadia et al. 2019](<https://arxiv.org/abs/1906.02530> "Can You Trust Your Model&#x27;s Uncertainty? Evaluating Predictive Uncertainty Under Dataset Shift; NeurIPS").

<a id="software-and-references"></a>

## Software

- **scikit-learn** - Splitters, scoring functions and pipelines support evaluation workflows. The chosen split, search and scorer still determine what is estimated. [docs](<https://scikit-learn.org/stable/modules/cross_validation.html>), [docs](<https://scikit-learn.org/stable/modules/model_evaluation.html>), [docs](<https://scikit-learn.org/stable/common_pitfalls.html>).
- **rliable** - Implementation of interval estimates, interquartile means, probability of improvement and performance profiles for benchmark score arrays. Run/task structure and score normalization remain explicit inputs to the analysis. [code](<https://github.com/google-research/rliable>).
- **baycomp** - Bayesian comparison tools for classifiers on one or multiple datasets; returns probabilities for superiority, practical equivalence and inferiority. The caller supplies the equivalence threshold. [docs](<https://baycomp.readthedocs.io/en/latest/>).

## Related topics

- [AutoML](https://mlpapers.org/automl/)
- [Feature selection](https://mlpapers.org/feature-selection/)
- [Anomaly detection](https://mlpapers.org/anomaly-detection/)
- [Bayesian inference](https://mlpapers.org/bayesian-inference/)
- [Interpretability](https://mlpapers.org/interpretability/)
- [Transfer learning](https://mlpapers.org/transfer-learning/)
