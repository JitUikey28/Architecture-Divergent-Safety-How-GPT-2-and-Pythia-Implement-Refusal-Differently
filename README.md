# Architecture-Divergent Safety: Mechanistic Interpretability of Refusal

This repository contains the implementation of a multi-stage mechanistic audit investigating how safety behaviors—specifically refusal—are represented within different neural architectures. This research challenges the assumption of "universal" safety circuits.

## 🚀 Research Overview
Traditional safety assessments rely on behavioral outputs, which can conceal underlying risks like reward hacking.This project utilizes **Mechanistic Interpretability** to move beyond the "black box" view of LLMs:
* **Localization**: Identifies that refusal is primarily localized in MLP sublayers rather than attention mechanisms.
* **Causal Validation**: Uses activation steering to prove that specific layers control model compliance or refusal.
* **Mapping**: Provides a microscopic view of safety by isolating sparse neuronal circuits.

---

## 🛠 Project Structure: The 3-Stage Pipeline

The codebase is organized into three experiments corresponding to the stages mentioned in the paper. All scripts share the same random seeds (42, 123, 456) to ensure reproducibility.

### Stage 1: Differential Safety Localization
**Goal**: Identify "Specialist Layers" with the highest differential contribution to harmful vs. harmless inputs.
* **Method**: Systematic **Zero Ablation** of MLP and Attention components.
* **Key Metric**: **Safety Shift Score (SSS)**, where an SSS > 1 indicates a disproportionate impact on harmful inputs.
* **Implementation**: `Stage 1 Discovery & Localization.ipynb`

### Stage 2: Causal Activation Steering
**Goal**: Test if the identified layers causally control refusal behavior through representational geometry.
* **Method**: Constructing **Refusal Direction Vectors** ($v = \mu_{harmful} - \mu_{harmless}$).
* **Intervention**: Applying positive and negative steering ($\pm \alpha v$) to benign prompts to observe behavior shifts.
* **Implementation**: `Stage 2 Causal Verification.ipynb`

### Stage 3: Sparse Neuron-Level Analysis
**Goal**: Refine localization from the layer level down to individual neurons.
* **Method**: Ranking neurons by differential activation and performing sparse ablation on the top-k (5 or 10) neurons.
* **Insight**: Ablating only a small ensemble of neurons can significantly shift refusal logits, confirming sparse but structured circuitry.
* **Implementation**: `Stage 3 High Resolution Neuron Mapping.ipynb`

---

## 📊 Key Findings

* **Architectural Divergence**: GPT-2 Small shows strong bidirectional control, while larger models or Pythia variants exhibit unstable or inverse steering patterns.
* **Depth Migration**: Safety mechanisms tend to be pushed to later layers as model size increases.
* **Specialist Layers**:
    * **GPT-2 Small**: Layer 11 (SSS: 4.822)
    * **Pythia-1B**: Layer 0 (SSS: 7.540) 
    * **GPT-2 Large**: Layer 35 (SSS: 1.458) 

---

## 💻 Setup and Usage

### Prerequisites
* T4 GPU (At least 16 GB RAM)
* PyTorch
* TransformerLens (recommended for activation patching and ablation)
* Datasets: The experiments use a custom prompt dataset covering Safety, IOI, Fact, and Math tasks.
