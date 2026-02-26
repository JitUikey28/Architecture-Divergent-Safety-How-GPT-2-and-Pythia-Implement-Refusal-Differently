# Architecture-Divergent Safety How GPT-2 and Pythia Implement Refusal Differently

Official implementation of the three-stage experimental pipeline from the paper:  
**"Architecture-Divergent Safety: How GPT-2 and Pythia Implement Refusal Differently"** *Jitesh Uikey et al.*

---

## 🧠 Overview
This repository investigates whether refusal behavior in transformer language models follows a universal mechanistic structure or varies across architectures. We conduct a multi-stage mechanistic audit across:

* **GPT-2:** Small, Medium, Large
* **Pythia:** 410M, 1B

The pipeline progressively localizes, validates, and refines safety circuitry using:
1.  **Differential Localization** (Layer-Level Audit)
2.  **Causal Activation Steering** (Subspace Intervention)
3.  **Sparse Neuron-Level Analysis** (Microscopic Circuit Mapping)

We introduce the **Safety Shift Score (SSS)** to quantify safety specialization.

---

## ⚙️ Reproducibility
All experiments use fixed random seeds: `[42, 123, 456]`

* **Averaging:** Results are averaged across seeds.
* **Error bars:** 95% confidence intervals computed via standard error.
* **Mode:** Models evaluated in inference mode (no fine-tuning).

---

## 📊 Stage 1 — Differential Safety Localization
**Goal:** Identify safety-specialized layers using systematic zero ablation.

* **Refusal Logit Difference:** $$R(p) = \text{logit}(\text{"I"}) - \text{logit}(\text{"Sure"})$$
    Captures the internal preference between refusal and compliance.
* **Safety Shift Score (SSS):** $$SSS = \frac{|\Delta R_{\text{harmful}}|}{|\Delta R_{\text{harmless}}| + \epsilon}$$
    * **SSS > 1:** Indicates a safety-specialized component.
    * **Specialist Layer:** The MLP layer with the highest mean SSS.

**Implementation:** `Stage 1 Discovery & Localization.ipynb`

---

## 🧭 Stage 2 — Causal Activation Steering
**Goal:** Test whether Specialist Layers causally control refusal behavior.

* **Steering Vector ($v$):** $v = \mu_{\text{harmful}} - \mu_{\text{harmless}}$ (computed at the Specialist Layer).
* **Intervention:**
    * Positive: $h' = h + \alpha v$
    * Negative: $h' = h - \alpha v$
    * Scale: $\alpha = 1.0$

**Implementation:** `Stage 2 Causal Verification.ipynb`

---

## 🧩 Stage 3 — Sparse Neuron-Level Analysis
**Goal:** Refine safety localization down to individual neurons.

1.  **Extract:** Post-MLP activations.
2.  **Differential:** $d_i = \mu_{\text{harmful},i} - \mu_{\text{harmless},i}$
3.  **Rank:** Select top-$k$ neurons ($k = 5, 10$) by $|d_i|$.
4.  **Ablate:** Zero only those neurons and recompute $R$ and $SSS$.

**Implementation:** `Stage 3 High Resolution Neuron Mapping.ipynb`

---

## 🧪 Prompt Dataset
Harmful and harmless prompts are paired (52 total) to isolate safety-specific degradation from general utility loss.

| Task | Harmful | Harmless |
| :--- | :--- | :--- |
| Safety | 9 | 9 |
| IOI | 6 | 6 |
| Fact | 6 | 6 |
| Math | 5 | 5 |
| **Total** | **26** | **26** |

---

## 📈 Key Findings
Results demonstrate that refusal geometry is architecture-dependent rather than universal.

| Model | Specialist Layer | Steering Behavior |
| :--- | :--- | :--- |
| **GPT-2 Small** | Late layer | Strong bidirectional |
| **GPT-2 Medium** | Early layer | Partial |
| **GPT-2 Large** | Deep layer | Unstable / inverse |
| **Pythia-410M** | Mid | Inconsistent |
| **Pythia-1B** | Early | Suppression-dominant |

---

## 🔬 Research Contributions
* **Architecture-aware safety audits.**
* **Refusal direction replication** across model families.
* **Sparse causal circuit identification.**
* **Cross-family mechanistic comparison** challenging the assumption of universal refusal circuits.

