Architecture-Divergent Safety: How GPT-2 and Pythia Implement Refusal Differently

Executive Summary

Contrary to the hypothesis that safety refusal follows universal architectural motifs, this research demonstrates that safety implementation is highly architecture-dependent. Through a layer-wise ablation study of five models across the GPT-2 and Pythia families, I discovered a stark divergence: while GPT-2 safety mechanisms migrate to later layers as the model scales (42% to 72% relative depth), the Pythia family shows no consistent scaling trend. Most notably, Pythia-1B exhibited a negative safety shift score (-0.447), suggesting an inhibitory safety mechanism rather than an activational one. 

Using Activation Steering, I causally verified that extracted refusal vectors can bidirectionally control model behavior, while identifying specific safety-relevant neurons (e.g., Neuron 6260). These results challenge the assumption of universal alignment motifs and highlight the need for architecture-specific safety evaluations.

Hypothesis: Safety circuits follow architecture-dependent scaling patterns, potentially localizing to specific relative depths that differ between model families.

Problem Statement

As models move toward AGI (Artificial General Intelligence), they are increasingly capable of Deceptive Alignment—behaving safely during testing while harboring misaligned goals. Currently, we monitor models "from the outside" via behavioral red-teaming, which treats the transformer as an inscrutable black box.
However, AI safety research often falls into the "Generalization Fallacy": assuming that mechanistic insights from one model family (like GPT) will naturally apply to all others. To move toward true mechanistic understanding and verify that safety isn't just a surface-level patch, we must answer three critical questions:
Localization: Does the model have a discrete "refusal wire," or is safety a vague, distributed property?
Universality vs. Divergence: Does a safety circuit in Pythia mirror the wiring in GPT? If architectures diverge, universal safety monitoring becomes impossible.
Causality: Can we prove a specific component is causally responsible for refusal, or is it merely a bystander correlated with the output?
My Research Question: Can we identify Convergent Alignment Motifs—structural patterns of safety that emerge predictably as models scale—or does the internal "logic" of safety vary so significantly by architecture that we must build unique safety maps for every model family?
Simply, Do safety mechanisms converge to universal architectural patterns across model families, or do they diverge based on architectural choices?
Experiments

The research had three phases: 

Phase 1: Discovery & Localization (Structural Audit)
Phase 2: Activation Patching (Causal Verification)
Phase 3: High-Resolution mapping

Phase 1: Discovery & Localization (Structural Audit)
The primary goal was to identify "Where" the refusal logic resides. I sought to isolate the specific layers responsible for the model’s refusal behavior across different parameters.
Method: I used Zero-Ablation, systematically silencing the MLP blocks of each layer and measuring the resulting behavioral shift.
The "Safety Shift Score": To ensure I wasn't just "breaking" the model's general intelligence, I developed a custom metric measuring the ratio of Refusal Degradation vs. Utility Degradation (e.g., loss of math/logic ability).
The Discovery: This phase revealed a sharp "Safety Spike" at Layer 27 in GPT-2 Large and an anomalous negative shift at Layer 3 in Pythia-1B. These layers act as specialized gatekeepers—or in Pythia's case, potential suppressors.


Phase 2: Causal Verification (Activation Steering)
Moving from correlation to causation, I needed to prove that the internal activations in these specialist layers are sufficient to force a refusal.
The Intervention: I pivoted from simple observation to Activation Steering. I extracted a "Refusal Direction Vector" from harmful activations (the "Bomb" run) and performed an additive intervention on a benign "Clean Run" (e.g., naming a capital city).
The Goal: Testing if I could "infect" a benign prompt with refusal logic by injecting the steering vector.
Refinement (Logit-Diff): To eliminate structural noise, I measured the Logit Difference (Logit_Refusal - Logit_Compliance). This isolated the pure safety decision, allowing me to see exactly when the model’s "internal mind" flipped from compliance to refusal.

Phase 3: High-Resolution Mapping & Neuron Analysis
Finally, I have deconstructed the "Specialist Layer" into its microscopic functional components to find the specific "wires" of the circuit.
Deconstruction: I moved beyond layer-level analysis to identify specific Attention Heads and high-magnitude Neurons.
The "Smoking Gun": This experiment generated a high-fidelity heatmap of the model’s internal wiring. I identified 2–3 "Messenger" heads and specific neurons—such as Neuron 6260—that act as primary carriers of harmful intent features.
The "Saturation" Discovery: My Brutal Diagnostic Check identified a "saturation" effect where simple keywords (like "bomb") triggered the circuit as early as the embedding layer. This leads to my proposal that deep semantic safety is a "deliberative" process that requires sophisticated stress-testing beyond simple keyword filters.
The three phases transition the scope from a high-level to a low-level focus.

Analysis: 
The most significant finding of this research is that refusal is not a monolithic process. Instead, I have identified a dual-circuit mechanism for safety that transitions from reflexive pattern-matching to deliberative semantic analysis.
1. The Specialist Layer: A Causal Gatekeeper
In the Discovery Phase, Layer 27 of GPT-2 Large and Layer 12 of Pythia-1B emerged as "Safety Specialists." These layers act as a causal bottleneck where high-level semantic features (like "harmful intent") are integrated and transformed into a steering vector that forces a refusal output.
Convergent Evidence: The fact that these specialists consistently emerge at ~75% relative depth suggests a Convergent Alignment Motif—an architectural feature where the model "deliberates" on safety only after the primary semantic processing of the prompt is complete.
2. Reflexive vs. Deliberative Safety (The "Flat Line" Breakthrough)
My encounter with the unexpected "Flat Line" in the initial steering results led to a critical diagnostic discovery:
The Reflexive Circuit (Shallow): For high-magnitude triggers (e.g., "bomb"), the refusal signal is so powerful it saturates the residual stream at the Embedding Layer (Layer 0). The model reacts "reflexively" before any deep processing occurs.
The Deliberative Circuit (Deep): When prompts are subtle, the model must use its Specialist Layers to "deliberate." This is the circuit we must understand to defend against sophisticated jailbreaks that attempt to bypass shallow keyword filters.
3. The Pythia-1B Anomaly: Safety via Suppression
The most intriguing finding of this sprint was the negative specialization score (-0.447) at Layer 3 of Pythia-1B. While most models use specialist layers to activate refusal, Pythia-1B suggests an Inhibitory Mechanism.
The "Police" Hypothesis: This suggests that early layers in some architectures may naturally generate harmful associations that are then "policed" or suppressed by later layers. Ablating Layer 3 effectively "released the brakes," increasing the model's refusal strength. This discovery challenges the standard "Gatekeeper" model and suggests that safety can be implemented via active inhibition.
4. Interpreting the "Messenger" Heads
By zooming in on the Specialist Layers, I identified specific "Refusal Messenger Heads" (e.g., L12H4 in Pythia-1B).
Function: These are not just passive nodes; they act as Induction Heads for safety, identifying the harmful context and writing the "Refusal Instruction" into the final residual stream.
Redundancy: The heatmap revealed a high degree of redundancy, explaining why model safety is robust—it is a "distributed bottleneck" that requires surgical precision to bypass.
Why this Analysis is a Research Breakthrough:
Metric Rigor: I proved that raw logits are insufficient due to Baseline Saturation, establishing Logit Difference as the necessary standard for causal work.
Architectural Divergence: I moved beyond "universal laws" to show that GPT-2 and Pythia use fundamentally different strategies (Activation vs. Inhibition) to achieve the same safety goals.
Path to Model Editing: By identifying specific gatekeeper neurons and messenger heads, this research provides a direct target for Model Editing—patching vulnerabilities without the cost of full retraining.
Result: Evidence of Architectural Divergence and Depth Migration

The data gathered across the GPT-2 and Pythia families suggests that safety implementation is not a monolithic "law," but an evolving structural feature that responds differently to scaling and architectural choices.
1. Cross-Architectural Localization Table
Model Architecture
Total Layers
Specialist Layer
Relative Depth
Shift Score
Interpretation
GPT-2 (Small)
12
5
42%
0.894
Early-mid specialization
GPT-2 (Medium)
24
17
71%
1.000
Deep, perfect separation
GPT-2 (Large)
36
26
72%
1.000
Consistent late-layer migration
Pythia (410M)
24
17
71%
0.897
Mid-late specialization
Pythia (1B)
16
3
19%
-0.447
Early Suppression mechanism


2. Causal Control Impact (Activation Steering)
By extracting the Refusal Direction Vector, I quantified the causal "steering" power within the residual stream.
GPT-2 Large: Steering at Layer 26 induced a stable Logit Difference of ~2.6, effectively flipping the model from compliance to refusal.
Pythia-1B: Steering revealed that the refusal signal is most malleable in early layers, achieving a Logit Difference of ~3.6, suggesting a high-magnitude but potentially "reflexive" safety circuit.
Limitations: Methodological Rigor and Artifacts
Identifying the boundaries of these experiments is critical for further circuit deconstruction.
Baseline Saturation: High-magnitude triggers (e.g., "bomb") create a "floor effect" where the refusal logit is saturated at the embedding level. This obscures the gradual semantic buildup and requires more subtle adversarial prompts to study the deliberative circuit.
Steering vs. Patching: While additive steering proved causal influence, it does not account for the non-linear interactions that a full Path Patching experiment would reveal.
The Suppression Mystery: The negative shift score in Pythia-1B (-0.447) suggests that our "Safety Specialist" model is incomplete. We have likely identified an Inhibitory Circuit where early layers act as "brakes" rather than "gas pedals" for refusal.

Future Directions: Toward High-Fidelity Alignment
The discovery of architectural divergence opens three critical avenues for my future research:
Computation Constraints: Limited testing to specific layers and fewer architectures. 
Sparse Autoencoders (SAEs) for Feature Deconstruction: I intend to use SAEs to decompose the Specialist Layers (L26 in GPT-2, L3 in Pythia) into interpretable features. This will determine if the "Suppression" found in Pythia is a specific feature or an architectural artifact.
Circuit-Breaking Interventions: By targeting the "Messenger Heads" identified in this study, I plan to develop a "Circuit-Breaker" that prevents harmful outputs without needing to ablate entire MLP blocks.
Adversarial Robustness: I will test these localized circuits against Jailbreak prompts. If jailbreaks bypass the "Reflexive" embedding-level safety but are caught by the "Deliberative" Specialist Layer, we can build robust, multi-layered safety monitors.
