# Machine Learning and the Robustness of Causal Estimates

This repository contains the research report and computational work for **Machine Learning and the Robustness of Causal Estimates: Evidence from Re-Estimated Studies in African Development**.

The project uses double/debiased machine learning (DML) to revisit three influential studies in African political economy and development. The central question is whether published estimates remain stable when observed-covariate adjustment is made more flexible while the original research design, treatment, estimand, fixed effects, instrument, clustering, and inferential structure are preserved.

Read the complete [research report](Econ_MA.pdf) for the motivation, methodology, results, and references.

## Project overview

DML combines cross-fitting with orthogonal estimating equations. Machine-learning models estimate nuisance functions such as conditional outcomes, treatments, and instruments on training folds; treatment effects are then estimated from held-out observations. This limits overfitting and first-order regularization bias without treating prediction as a substitute for research design.

The project applies this framework to three settings:

| Study | Research design | DML application |
|---|---|---|
| Nunn and Wantchekon (2011) | Historical cross-sectional analysis and instrumental variables | Partially linear DML for Table 3 and DML-IV for Table 6, with ethnicity and district clustered inference |
| Depetris-Chauvin, Durante, and Campante (2020) | Event timing around national-team football matches | Fixed-effect-residualized partially linear DML for the principal Table 2 and Table 4 outcomes |
| Weigel (2020) | Neighborhood-level randomized property-tax campaign | Design-aware AIPW/DML-IRM and PLR-DML with neighborhood folds and randomization inference |

Across the notebooks, nuisance learners include lasso, elastic net, boosting, single trees, random forests, neural networks, and ensembles. Learners are compared using out-of-fold nuisance performance and design-relevant diagnostics, not the sign or statistical significance of the treatment estimate.

## Main findings

- **Slave-trade exposure and trust:** The partially linear Table 3 estimates preserve the negative association between ancestral slave-trade exposure and present-day trust under stable regularized, ensemble, conservative, and ethnicity-cluster specifications. Exact magnitudes remain sensitive to learner and fold construction.
- **Slave-trade instrumental variables:** The principal lasso and ensemble DML-IV estimates support the published direction, but ethnicity-cluster and conservative specifications reveal weak residualized first stages and unstable score denominators. The IV evidence is therefore more fragile than the partially linear results.
- **Football victories and national identity:** The fixed-effect-residualized DML estimates remain close to the published and replicated fixed-effect estimates. This supports robustness to nonlinear adjustment for the observed controls but does not validate the event-timing assumptions themselves.
- **Taxation and participation:** Both design-aware DML routes broadly preserve the positive participation response to the randomized tax campaign. Evidence is strongest for town hall attendance, composite outcomes, the participation index, and cost measures; evaluation submission weakens after flexible adjustment and multiple-testing correction.

These exercises evaluate robustness to flexible adjustment for observed variables. They do not remove unobserved confounding, validate an exclusion restriction, prove that event timing is exogenous, or replace random assignment and design-consistent inference.

## Primary analysis notebooks

### [DML_Table3_Nunn_Wantchekon_Python.ipynb](DML_Table3_Nunn_Wantchekon_Python.ipynb)

Re-estimates the five trust outcomes from Nunn and Wantchekon's Table 3 using a partially linear DML model. The notebook includes:

- outcome-specific complete-case samples;
- flexible outcome and treatment nuisance functions;
- 100 repeated two-fold cross-fitting splits;
- two-way clustered inference by Murdock ethnicity and district;
- nuisance-performance learner selection;
- ethnicity-cluster cross-fitting; and
- conservative-learner and five-fold sensitivity analyses.

### [DML_Table6_IV_Nunn_Wantchekon_Python.ipynb](DML_Table6_IV_Nunn_Wantchekon_Python.ipynb)

Extends the Nunn-Wantchekon analysis to the Table 6 instrumental-variables design. Slave-trade exposure, `ln_export_area`, is instrumented with distance from the coast, `distsea`. The notebook separately residualizes the outcome, endogenous treatment, and instrument and reports:

- a linear IV benchmark validation;
- repeated cross-fitted DML-IV estimates;
- residualized first-stage and denominator diagnostics;
- two-way clustered IV-score inference;
- ethnicity-cluster cross-fitting; and
- conservative DML-IV sensitivity analyses.

### [DML_Campante_BuildingNations_Table2_Table4_Python.ipynb](DML_Campante_BuildingNations_Table2_Table4_Python.ipynb)

Re-estimates the main individual-level findings from Depetris-Chauvin, Durante, and Campante. It first uses alternating projections to remove the original country-match, language-year, and calendar fixed effects, then applies repeated PLR-DML to the residualized variables. The notebook contains baseline replication, fixed-effect convergence checks, country-year clustered inference, learner comparisons, multiple-testing diagnostics, and singleton-sample sensitivity.

### [DML_Weigel_ParticipationDividend_TableIV_Python.ipynb](DML_Weigel_ParticipationDividend_TableIV_Python.ipynb)

Re-estimates Weigel's Table IV intention-to-treat effects while respecting treatment assignment at neighborhood `a7`. It includes:

- audited reconstruction of the analysis data;
- an executable pre-treatment feature ledger;
- conventional stratified OLS replication;
- assignment-propensity and fold-support checks;
- neighborhood- and stratum-aware folds;
- Route A randomization-aware AIPW/DML-IRM;
- Route B design-propensity PLR-DML;
- neighborhood-clustered analytic inference;
- 5,000-draw neighborhood-within-stratum randomization inference; and
- multiple-testing adjustment for the primary outcome family.

The original replication package identifies tax-authority name matching as confidential. This project does not reconstruct or expose those confidential records.


## Runtime requirements

The main notebooks use Python 3 and a Jupyter-compatible environment.

### GPU-oriented notebooks

The Nunn-Wantchekon DML notebooks and the Campante notebook are designed for a Google Colab CUDA runtime. Their setup cells use:

- NumPy and pandas;
- RAPIDS cuDF and cuML;
- CuPy;
- XGBoost;
- PyTorch;
- SciPy; and
- Matplotlib.

Select a GPU runtime before executing their setup cells. The notebooks check CUDA availability and install missing RAPIDS packages in Colab when necessary. A runtime restart may be required after installation.


## Reproducing the analysis

1. Clone or download the repository.
2. Obtain the original replication packages and place them in the expected directories.
3. Configure the relevant project-root environment variable.
4. Choose the appropriate CPU or CUDA runtime.
5. Open a notebook and run its setup, source-validation, and pre-estimation checks first.
6. Confirm that the conventional benchmark replication is satisfactory before interpreting DML results.
7. Run outcome cells individually. Completed outcome-level checkpoints allow interrupted sessions to resume without rerunning every model.
8. Use full-run settings for reported results. Smoke-test settings verify code flow only and should not be interpreted as final estimates.

Full runs are computationally expensive. The Nunn and Campante analyses use repeated cross-fitting across several learner families, while the Weigel final inference refits nuisance models across 5,000 randomization draws.
