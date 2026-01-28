# Bayesian Model of Multimodal Perception: Individual differences in the Ventriloquist Illusion after-effect

**Type:** University course project (Computational Cognitive Science 2, MSc in IT & Cognition, University of Copenhagen, Spring 2024)

**Authors:** Wera Kopytko & Miłosz Holeksa (equal contribution)

**Report (PDF):** see `paper_bayesian_model_of_multimodal_perception_ventriloquist_illusion.pdf` in this repository

**Original repository (full code):** [MiloszHoleksa/Bayesian-Analysis](https://github.com/MiloszHoleksa/Bayesian-Analysis). This repo is for a portfolio purposes.

---

## What is this project about?

Human perception is *multimodal*: the brain combines information from different senses (e.g. vision and audition) to form a single percept. A classic example is the **Ventriloquist Illusion**, where the perceived sound location is pulled towards the visual stimulus location (vision tends to be more reliable for spatial localization).

This project focuses on the **after-effect**: how exposure to audio-visual (AV) trials changes perception in the following audio-only (A) trials. We build **Bayesian regression models** to quantify:

- how strongly the AV illusion predicts the A-trial after-effect
- how this relationship differs across people
- whether an individual-differences (multimodal) model predicts better than a pooled baseline model

---

## Data (multimodal perception experiment)

We reuse publicly available data from a prior study (Kayser et al.; dataset corresponds to experiment 11). The dataset includes **19 participants** and trial sequences containing:

- **AV trials (audio + visual):** measure the *Ventriloquist Illusion* as the shift of perceived sound toward the visual stimulus (`Shift_AV`)
- **A trials (audio-only, following AV):** measure the *after-effect* in sound localization (`Shift_A`)
- **V trials (visual-only):** used for balancing modality load; discarded in the original study and also in our analysis

---

## Modeling approach

We compare two Bayesian models for predicting the after-effect `Shift_A`.

### 1) Baseline (pooled) model

A simple regression shared across all participants:

$$
Shift_A \sim \mathcal{N}(\mu,\sigma), \quad \mu = \alpha + \beta \cdot Shift_{AV}
$$

Priors:

* $\alpha \sim \mathcal{N}(0,1)$
* $\beta \sim \mathcal{N}(0,1)$
* $\sigma \sim \text{Exponential}(1)$

**Interpretation:** one global relationship between AV shift and A-trial after-effect.

---

### 2) Multimodal (individual differences) model

A participant-specific model that adds:

- **individual intercept** $ \alpha_i $: initial receptivity to the after-effect
- **trial index effect** $beta_{oi}$: change in receptivity over time (trial number)
- **participant-specific AV coupling** $beta_{si}$

$Shift_A \sim \mathcal{N}(\mu,\sigma), \quad \mu = \alpha_i + \beta_{oi}\cdot obs + \beta_{si}\cdot Shift_{AV}$

Priors:

- $\alpha_i \sim \mathcal{N}(0,1)$
- $\beta_{oi} \sim \mathcal{N}(0,1)$
- $\beta_{si} \sim \mathcal{N}(0,1)$
- $\sigma \sim \text{Exponential}(1)$

**Interpretation:**

- $\alpha_i$ = “starting level” of after-effect susceptibility per participant
- $\beta_{oi}$ = how susceptibility changes across trials (learning/adaptation / drift)

---

## Inference

We performed Bayesian inference for both models using PyMC, with:

- 4 chains
- 4000 tuning samples + 4000 draws

---

## Hypotheses

We evaluated three hypotheses:

- **H1:** Initial individual receptivity to the after-effect varies across participants $\alpha_i$ differs)
- **H2:** The rate of change of receptivity varies across participants $\beta_{oi}$ differs)
- **H3:** The Multimodal (individual-differences) model predicts better than the Baseline model

We assess differences using posterior distributions and 95% HDIs (rather than frequentist tests).

---

## Results

### Baseline model posterior (pooled)

- $\alpha$ mean = **0.174**, 95% HDI **(-0.395, 0.805)**
- $\beta$ mean = **0.135**, 95% HDI **(0.068, 0.205)**

### Individual differences (Multimodal model)

Across participants, the posterior intervals for $\alpha_i$and $\\beta_{oi}$ show **non-overlapping 95% HDIs**, indicating meaningful between-person differences in:

- initial susceptibility $\alpha_i$
- trial-dependent change $\beta_{oi}$

***Note***: *Participant-level parameter plots and exact values are provided in the report appendix.*

---

## Model comparison (predictive performance)

We compare models with **WAIC / LOO-style metrics**:

| model      | rank | elpd_loo | p_loo | elpd_diff | weight |
| ---------- | ---- | -------- | ----- | --------- | ------ |
| multimodal | 0    | -1712.88 | 59.90 | 0.0       | 0.59   |
| baseline   | 1    | -1721.80 | 3.85  | 8.92      | 0.40   |

Interpretation:

- The Multimodal model has better predictive performance (better WAIC rank; higher model weight), despite higher complexity (p_loo).

---

## Key takeaway

A Bayesian model that explicitly represents **individual differences** provides a better account of the ventriloquist after-effect than a pooled regression. This supports the idea (aligned with Predictive Processing) that people differ in their internal priors / receptivity in multimodal perception.

---

## Limitations & possible extensions

- We used **linear** relationships; effects may be non-linear.
- Parameters like $\alpha$ and $\beta_{oi}$ could be influenced by unmodeled factors; adding covariates could explain more variance.
