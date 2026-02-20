Architecture-Divergent Safety: How GPT-2 and Pythia Implement Refusal Differently


Executive Summary:

This research conducts a systematic mechanistic interpretability study on the GPT-2 and Pythia model families to understand how refusal behavior is encoded within their neural networks and how it can potentially be manipulated. Our primary metric for quantifying refusal tendency was the logit difference between refusal-indicating tokens (e.g., "I") and compliance-indicating tokens (e.g., "Sure"). A higher positive logit difference indicates a stronger refusal tendency.

1. Models Analyzed
We analyzed a range of models across two prominent architectural families:

GPT-2 Family: gpt2-small (124M parameters), gpt2-medium (355M parameters), gpt2-large (774M parameters).
Pythia Family: pythia-410m (410M parameters), pythia-1b (1 Billion parameters).
This selection allowed for a comparative analysis of refusal mechanisms across different scales and training methodologies.

2. Ablation Study & Specialist Layers
Our initial phase involved a comprehensive ablation sweep, where we systematically zeroed out activations in Attention heads and MLP layers across sampled layers for various prompts. This identified specialist layers—layers whose ablation significantly altered the model's refusal behavior.

GPT-2 Small (124M): Specialist Layer 5 (Vector magnitude: 43.4556)
GPT-2 Medium (355M): Specialist Layer 17 (Vector magnitude: 117.6844)
GPT-2 Large (774M): Specialist Layer 0 (Vector magnitude: 17.4432)
Pythia 410M (410M): Specialist Layer 0 (Vector magnitude: 10.0194)
Pythia 1B (1B): Specialist Layer 0 (Vector magnitude: 17.4829)
Interestingly, larger models, particularly in the Pythia family, often showed initial layers as their 'specialist' layers, suggesting early processing of refusal-related information.

3. Component Analysis: MLP vs. Attention
One of the most striking findings from the ablation study is the differential impact of MLP (Multi-Layer Perceptron) and Attention components on refusal behavior:

MLP layers consistently demonstrate a significantly higher average impact on refusal behavior (abs_delta of 0.6947) compared to Attention heads (abs_delta of 0.0000). This implies that the MLP sublayers are the primary computational units involved in encoding and processing information pertinent to generating refusal responses. Ablating attention heads in this setup had negligible impact, suggesting their role might be more about contextualizing input rather than directly computing refusal intent in these specific scenarios

4. Steering Effectiveness (Causal Intervention)
Following the identification of specialist layers, we calculated steering vectors—the difference between mean activations for harmful vs. harmless prompts at the specialist layer's residual stream output. We then tested the causal effect of adding or subtracting these vectors on benign prompts to induce or suppress refusal.

Average Positive Steering Effect: 0.4986 (Successfully induces refusal in benign prompts). This indicates that by intervening in the model's activation space at specific layers, we can causally influence its tendency to refuse. For models like gpt2-medium and pythia-410m, positive steering led to a substantial increase in refusal logit difference (e.g., gpt2-medium: +1.3780).
Negative Steering Effects were mixed but generally reduced refusal, with gpt2-small showing a significant decrease (-0.5173).
These results provide strong causal evidence that refusal behavior is encoded in a steerable direction within the model's internal representations.

5. Top Neurons for Refusal
Drilling down to the neuron level, we identified individual MLP neurons that showed the largest differential activation between harmful and harmless prompts within the specialist layers. These are considered "top neurons" potentially involved in processing refusal.

For example:

gpt2-small: Neuron 756 in Layer 5 (Differential: -0.6870, meaning it activates more for harmless prompts or less for harmful ones).
gpt2-large: Neuron 1867 in Layer 0 (Differential: 0.9814, meaning it activates more for harmful prompts).

6. Sparse Ablation of Top Neurons
To verify the importance of these top neurons, we performed sparse ablations, zeroing out the activations of the top 10 identified neurons in the specialist layers when processing harmful prompts.

Ablating these top 10 neurons caused an average change of 0.0122 in logit difference, predominantly reducing refusal. While the average effect might seem modest, this demonstrates that even a small subset of neurons can contribute to the model's refusal behavior, offering a pathway for targeted interventions.

7. Attention Head Analysis
Our analysis of individual attention heads, particularly in gpt2-small and pythia-410m at their respective specialist layers, showed negligible differential impact. This reinforces the finding that MLPs are the dominant component for refusal behavior in the context of our current setup.

8. Key Findings Summary
MLPs are the primary drivers of refusal behavior: Their ablation leads to significantly larger changes in refusal logit difference compared to attention heads.
Pythia models exhibit higher overall sensitivity to ablations, suggesting potentially more distributed or sensitive refusal mechanisms.
Refusal is causally steerable: We can predictably increase or decrease refusal tendencies by intervening with steering vectors at specific specialist layers.
Specific neurons contribute to refusal: A small set of highly active MLP neurons can be identified and, when ablated, influence refusal behavior.

Conclusion
This research provides strong evidence for the localized and steerable nature of refusal behavior in LLMs, primarily mediated by MLP layers. The ability to identify specialist layers and even individual neurons involved in refusal opens avenues for developing more robust and transparent safety interventions, allowing for fine-grained control over model alignment without retraining.
