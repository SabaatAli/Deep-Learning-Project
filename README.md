**Executive Summar**y
This enhanced report presents a structured and technically detailed study of a multi-label bioacoustic recognition pipeline developed for BirdCLEF+ 2026. The task is to process 60-second continuous soundscape recordings, divide each recording into twelve non-overlapping five-second windows, and estimate the presence of 234 target taxa for every window. The documented system combines frozen Perch V2 representations, selective state-space sequence models, metadata-aware contextual priors, class-specific MLP probes, a five-fold sound-event-detection branch, BirdNET V2.4, and percentile rank-space ensemble fusion.
The most important documented performance result is a public leaderboard score of 0.94727, compared with a previous best of 0.48813. Because the competition metric is macro-averaged ROC AUC, this value should be reported as an AUC/leaderboard score rather than ordinary classification accuracy. Expressed as a percentage, 0.94727 corresponds to 94.727% of the AUC scale; it does not mean that 94.727% of individual predictions were correct. The source report explicitly notes that the final configuration bundled several changes, so the individual contribution of each component was not isolated.

**1. Introduction and Research Motivation**
1.1 Background
Passive acoustic monitoring provides a scalable way to collect ecological observations without continuous human presence. Autonomous recording units can capture long periods of environmental audio, but the resulting data volume creates a severe annotation bottleneck. BirdCLEF+ frames this problem as machine listening: an automated system must identify which taxa are acoustically present in short temporal windows.
1.2 Why the Problem Is Difficult
•	Environmental recordings contain wind, rain, insects, water, aircraft and human-generated noise.
•	Multiple taxa can vocalise simultaneously, making the task multi-label rather than single-label.
•	Vocalisations can be short and sparse, so a five-second window may contain only a small informative event.
•	The label distribution is long-tailed, with some taxa having far fewer positive examples than common taxa.
•	The macro-AUC evaluation gives each supported class influence on the final metric, making rare-class coverage important.
1.3 Research Motivation
The central design principle is to avoid relearning general acoustic representations when a strong bird-domain backbone is already available. Instead, the project learns compact task-specific heads and sequence models over frozen embeddings and combines independent detectors at inference time.
2. Problem Definition and Objectives
Let a recording be divided into T = 12 windows and let C = 234 be the target taxonomy size. The model produces a score p(t,c) for each window t and class c. The competition evaluates class-wise ROC AUC and then averages across classes with positive support.
Conceptually:

**. Literature and Technical Foundations**
4.1 Domain-Specific Audio Embeddings
Perch V2 is used as a frozen domain-specific feature extractor. The motivation is that representations learned from bird vocalisations can encode useful acoustic distinctions that are difficult to learn from a modest downstream dataset.
4.2 Selective State-Space Models
State-space sequence models provide a mechanism for processing ordered observations while maintaining a hidden state. The project uses a simplified bidirectional selective SSM to model dependencies among the twelve soundscape windows.
4.3 Prototype-Based Classification
Instead of relying only on an unconstrained linear classifier, the LightProtoSSM head represents each class through a learnable prototype in the embedding space. Normalized similarity between a window representation and class prototypes provides class evidence.
4.4 Sound Event Detection and BirdNET
The final ensemble incorporates a five-fold SED branch and BirdNET V2.4. These sources provide complementary acoustic evidence and reduce dependence on a single score distribution.
4.5 Rank-Space Fusion
Probability scales from heterogeneous models are not necessarily comparable. The final system therefore converts scores into within-class percentile ranks and combines the ranks using fixed blend weights.

Macro-AUC = (1 / |C⁺|) × Σ AUC(c),  where C⁺ contains classes with positive support.
This distinction is important for reporting: a high AUC means the system tends to rank positive windows above negative windows. It is not equivalent to the percentage of labels classified correctly at a fixed threshold.
**
**12. Conclusion and Future Work****
This enhanced report documents a complete multi-model bioacoustic recognition system for BirdCLEF+ 2026. The architecture combines frozen domain-specific embeddings, temporal state-space modeling, prototype-based classification, contextual priors, independent SED and BirdNET detectors, and rank-space ensemble fusion. The documented final public leaderboard score is 0.94727, compared with a previous best of 0.48813.
The central lesson is that reliable label coverage, identifier alignment and evaluation-aware engineering can be as important as architectural complexity. The reported result should be described as a macro ROC-AUC leaderboard score, not as ordinary classification accuracy.
12.1 Future Work
•	Use grouped cross-validation by recording file.
•	Calibrate thresholds and blend weights from out-of-fold predictions.
•	Measure each architectural component through controlled ablation experiments.
•	Activate and independently evaluate configured regularizers such as mixup, focal loss and label smoothing.
•	Investigate learned per-class ensemble weights.
•	Use conservative pseudo-labelling for unannotated soundscapes.
•	Report per-class AUC and rare-class performance in addition to the macro average.
•	Add thresholded accuracy, precision, recall and F1 only when an explicit validation threshold is defined.
