# Student Submission Checklist (Lab 3)

Before submitting your Lab 3 assignment, ensure that **all items below are completed**. Submissions that do not follow this checklist may receive partial or no credit.

---

## 🔹 Repository and Branching

* [ ] The repository is correctly created on GitHub.
* [ ] All work is committed to **exactly one branch** named
  `firstname_U20230xxx`.
* [ ] **No work is pushed to `master`**.
* [ ] The correct branch is pushed to GitHub.

---

## 🔹 Notebook Submission

* [ ] Exactly **one** Jupyter Notebook (`.ipynb`) is submitted.
* [ ] The notebook is placed at the **root of the repository**.
* [ ] The notebook is named **exactly**:
  `lab3_results_<roll_number>.ipynb`.
* [ ] The notebook runs **top to bottom without errors**.
* [ ] All outputs (plots, tables, metrics) are visible in the notebook.

---

## 🔹 Sampler Usage

* [ ] The provided `sampler` package is used **without modification**.
* [ ] The sampler is initialized using your correct roll number `i`.
* [ ] Rewards are obtained **only** via `sampler.sample(j)`.
* [ ] No hard-coded or synthetic rewards are used.

---

## 🔹 Contextual Bandit Implementation

* [ ] User category is treated as the **context**.
* [ ] News category is treated as the **bandit arm**.
* [ ] The arm index mapping follows the specification in the lab handout.
* [ ] All three algorithms are implemented:

  * Epsilon-Greedy
  * Upper Confidence Bound (UCB)
  * SoftMax

---

## 🔹 Evaluation and Plots

* [ ] Classification accuracy is reported on `test_users.csv`.
* [ ] Reinforcement learning simulation is run for **T = 10,000 steps**.
* [ ] Plots include:

  * Average Reward vs. Time (per context)
  * Hyperparameter comparison plots
* [ ] All plots have labeled axes, legends, and titles.

---

## 🔹 README.md Requirements

* [ ] README.md is present at the repository root.
* [ ] It explains the overall approach and design decisions.
* [ ] It summarizes key results and observations.
* [ ] It includes clear instructions to reproduce the experiments.
* [ ] All external references (if any) are properly cited.

---

## Important Note

> Submissions that do not follow the specified branch name, notebook naming convention, or sampler usage rules may not be evaluated.


# Lab 3: Contextual Bandit-Based News Recommendation System  
**Name:** Aditya Masutey  
**Roll No.:** U20230094  
**Course:** Reinforcement Learning Fundamentals  

---

## 1. Project Overview

This project implements a **Contextual Multi-Armed Bandit (CMAB)** framework for personalized news recommendation.

The system integrates:
- A supervised learning classifier for user context detection
- Context-specific bandit algorithms for decision-making
- A reward sampler to simulate stochastic environment feedback
- A complete recommendation engine that outputs real news articles

The objective is to maximize cumulative reward over a time horizon of **T = 10,000 steps**.

---

## 2. Problem Formulation

The environment is modeled as:

- **3 Contexts (User Types):**
  - User1
  - User2
  - User3

- **4 News Categories (Arms per context):**
  - Entertainment
  - Education
  - Tech
  - Crime

- **Total Arms:** 3 × 4 = 12

### Arm Index Mapping

| Arm Index Range | User Context |
|-----------------|-------------|
| 0–3             | User1      |
| 4–7             | User2      |
| 8–11            | User3      |



Each context has its own reward distribution.

---

## 3. Data Preprocessing

- Missing values handled using **median imputation**
- No columns were removed
- Features scaled using **StandardScaler**
- Proper encoding applied to categorical features

This ensured stable training and consistent bandit performance.

---

## 4. User Classification (Context Detection)

### Model Used:
**Logistic Regression**
- Penalty: L1
- Solver: saga
- C = 0.5
- Multi-class: multinomial
- Max iterations: 1000

### Validation Accuracy:
**92.5%**

### Classification Report:

| Class  | Precision | Recall | F1-score |
|--------|----------|--------|----------|
| User1  | 0.91     | 0.88   | 0.89     |
| User2  | 0.99     | 0.91   | 0.95     |
| User3  | 0.88     | 1.00   | 0.94     |

The classifier achieved strong performance across all user categories and served as the **Context Detector** for the CMAB system.

---

## 5. Contextual Bandit Algorithms

Simulation horizon: **T = 10,000**

---

### 5.1 Epsilon-Greedy

Tested ε values:
- 1
- 0.01
- 0.05
- 0.1
- 0.3

**Best ε:** 0.01  
**Final Average Reward:** ≈ 6.47  

Observations:
- ε = 1 (pure exploration) performed poorly.
- Larger ε values caused excessive exploration.
- ε = 0.01 achieved best balance.
- Performance highly sensitive to ε.

Epsilon-Greedy exhibits **linear regret growth** due to constant exploration probability.

---

### 5.2 Upper Confidence Bound (UCB)

Tested C values:
- 0.5
- 1
- 2

**Best C:** 2  
**Final Average Reward:** ≈ 6.61 (Best Overall)

Observations:
- Very fast convergence.
- Stable learning curves.
- Robust to hyperparameter variation.
- Automatically reduces exploration as confidence increases.

UCB demonstrated **logarithmic regret growth**, making it the most sample-efficient algorithm.

---

### 5.3 SoftMax (τ = 1)

Temperature fixed at 1.0.

**Final Average Reward:** ≈ 5.93  

Observations:
- Smooth probabilistic selection.
- Slower convergence than UCB.
- Persistent exploration prevents full commitment to optimal arms.

SoftMax showed **sub-linear regret**, but underperformed compared to UCB.

---

## 6. Context-Level Analysis

Final approximate rewards per context:

| Context | Final Reward | Observation |
|----------|-------------|-------------|
| User2   | ~8.1        | Easiest to learn |
| User3   | ~6.1        | Moderate difficulty |
| User1   | ~5.6        | Lower reward ceiling |

User2 had the strongest reward signal, leading to faster convergence.

---

## 7. Performance Comparison

Ranking of best configurations:

1. **UCB (C = 2)** – 6.61  
2. ε-Greedy (ε = 0.01) – 6.47  
3. SoftMax (τ = 1.0) – 5.93  

UCB achieved highest cumulative reward and fastest convergence.

---

## 8. Recommendation Engine Results

The complete system was deployed on `test_users.csv`.

**Total Recommendations Generated:** 2000

### Predicted User Distribution

| User Type | Count |
|------------|-------|
| User2     | 693   |
| User3     | 654   |
| User1     | 653   |

### Recommended News Category Distribution

| Category       | Count |
|---------------|-------|
| Education     | 693   |
| Crime         | 654   |
| Entertainment | 653   |

### Cohort-Level Specialization Learned

| User  | Recommended Category |
|--------|---------------------|
| User1 | Entertainment        |
| User2 | Education            |
| User3 | Crime                |

The system successfully learned distinct preferences for each user type.

---

## 9. Key Findings

- UCB delivered best overall performance due to uncertainty-driven exploration.
- Epsilon-Greedy required careful tuning.
- SoftMax provided stable but slightly lower performance.
- Context-aware learning significantly improved reward over random selection.
- User2 exhibited the clearest reward structure.
- Strong context detection (92.5% accuracy) contributed to effective learning.

---

## 10. Conclusion

The Contextual Multi-Armed Bandit framework effectively combined supervised learning and reinforcement learning for personalized news recommendation.

Among the tested strategies, **UCB emerged as the most robust and sample-efficient algorithm**, achieving highest cumulative reward and fastest convergence.

The system successfully:

- Classified users accurately
- Learned context-specific reward distributions
- Specialized recommendations per cohort
- Delivered 2000 personalized article recommendations

This validates the effectiveness of contextual bandits in real-world recommendation systems.
