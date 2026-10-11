# FinGuard-Q: Quantum-Assisted Financial Fraud Investigation Prioritization

**Combining classical risk scoring with QAOA to explore investigation prioritization under limited review capacity.**

## Overview

Financial fraud investigation teams have limited time and resources. When many transactions are flagged as suspicious, deciding which cases to review first becomes an optimization problem.

FinGuard-Q explores a hybrid classical–quantum workflow: a classical machine-learning model assigns risk scores to transactions, and the Quantum Approximate Optimization Algorithm (QAOA) is used to explore the selection of high-priority cases under a review-time budget.

This project is an experimental prototype using synthetic transaction data. It does not claim demonstrated quantum advantage or production-ready fraud detection.

## Problem Statement

Given a set of candidate transactions, each with a relative priority score and an estimated review time, select a subset that maximizes total priority without exceeding the available review budget.

## System Architecture

1. **Data preparation:** Clean and inspect PaySim transaction data.
2. **Classical risk scoring:** Train and evaluate a Logistic Regression model using transaction step, type, and amount.
3. **Candidate selection:** Build a shortlist from model scores without using held-out labels to rank candidates.
4. **Quantum optimization:** Formulate binary candidate selection as a constrained optimization problem and explore it using Qiskit and QAOA.
5. **Evaluation:** Compare the QAOA output with greedy and exact classical baselines.

## Dataset

The initial experiment uses a downloaded subset of the synthetic PaySim dataset.

After removing one record with a missing fraud label, the dataset contained 28,296 records:

- 28,212 non-fraud transactions
- 84 fraud transactions
- Fraud prevalence: approximately 0.297%

The dataset is highly imbalanced, which makes precision and recall particularly important evaluation metrics.

## Classical Model Results

A Logistic Regression pipeline with class weighting was evaluated on a stratified test set.

| Metric | Result |
|---|---:|
| Average precision | 0.0324 |
| Precision | 0.0165 |
| Recall | 0.9048 |
| F1-score | 0.0324 |
| True positives | 19 |
| False positives | 1,132 |
| False negatives | 2 |
| True negatives | 5,921 |

The model identified 19 of 21 fraud cases in the test set but also generated many false positives. These results are an exploratory baseline, not evidence of production-level performance.

Detailed metrics: [`results/model_metrics.json`](results/model_metrics.json).

## Optimization Formulation

Let \(x_i \in \{0,1\}\) indicate whether candidate \(i\) is selected.

The objective is to maximize total candidate priority:

\[
\max \sum_i p_i x_i
\]

subject to the review-time budget:

\[
\sum_i t_i x_i \leq B
\]

where \(p_i\) is the relative priority, \(t_i\) is the assumed review time, and \(B\) is the review budget.

The constrained problem is converted into a penalized binary objective for the QAOA experiment.

## Initial Candidate Optimization

The initial three-candidate experiment uses the following relative priorities and illustrative review times:

| Candidate | Transaction ID | Priority | Review time |
|---|---:|---:|---:|
| A | 375 | 100 | 3 |
| B | 362 | 53 | 3 |
| C | 379 | 25 | 3 |

With a review budget of 6, exhaustive classical enumeration gives the exact optimum:

- **Selected candidates:** A + B
- **Total priority:** 153
- **Review time:** 6

These values are the reference baseline for the QAOA experiment. Priorities are relative shortlist scores, not calibrated fraud probabilities. Review times and budget are illustrative assumptions.

Candidate details: [`results/candidate_shortlist.csv`](results/candidate_shortlist.csv).

## Quantum Experiment

The quantum component uses Qiskit and the Qollab simulator to execute a QAOA circuit for the constrained candidate-selection problem.

The QAOA experiment is being developed and evaluated against the exact classical solution. Measured results, feasibility rates, and comparisons will be documented after the updated experiment runs.

## Repository Structure

```text
FinGuard-Q/
├── README.md
├── notebooks/
│   └── finguard_data_analysis.ipynb
├── results/
│   ├── model_metrics.json
│   └── candidate_shortlist.csv
└── ...
```

## Limitations

- PaySim is synthetic data and does not establish real-world performance.
- The current dataset is a small downloaded subset.
- The classical model produces many false positives.
- The optimization experiment uses only three candidates.
- Review times and priorities are illustrative.
- QAOA may return infeasible or suboptimal samples.
- No quantum advantage is claimed; results must be assessed against classical baselines.

## Reproducibility

The notebook in `notebooks/` contains the initial data-analysis and classical-model workflow. The quantum implementation is maintained in the Qollab project.

See the notebook and results files for the completed stages. Quantum execution instructions and measured results will be added after validation.

## Attribution

This effort is supported by Qollab & IonQ.

## License

This project is intended to be released under the MIT License.
