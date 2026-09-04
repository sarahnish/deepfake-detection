[← Back to Project Overview](https://github.com/sarahnish/deepfake-detection)

# Results

This directory contains the four principal evaluation figures from the final post-tuning experiment. The results compare a clean-only detector, a unified degradation-trained detector, a degradation-routed expert system, and oracle routing.

| Model | Overall test accuracy |
|---|---:|
| Clean-only | 70.58% |
| Unified | 98.11% |
| Routed | **98.42%** |
| Oracle | 98.43% |

The Stage-1 degradation router achieved **95.45% accuracy** on the 14,000-image test set.

The strongest finding is that training across degraded images produced most of the robustness improvement:

- Clean-only to unified: **+27.53 percentage points**
- Unified to routed: **+0.31 percentage points**
- Routed to oracle: **+0.01 percentage points**

## 1. End-to-End Detection Accuracy

![End-to-end detection accuracy](end-to-end-accuracy.png)

This figure compares all four systems overall and across the seven evaluation conditions:

- Clean
- Salt-and-pepper noise
- Gaussian noise
- Gaussian blur
- Motion blur
- Low resolution
- JPEG compression

The clean-only detector performs strongly on clean images but degrades substantially under several corruptions. The unified, routed, and oracle systems remain close to 98% accuracy across most conditions.

## 2. Degradation-Router Performance

![Degradation-router training curve and confusion matrix](router-performance.png)

The router reached **95.45% degradation-type test accuracy**. Salt-and-pepper noise, Gaussian noise, and motion blur were identified almost perfectly.

The main remaining confusion occurred between Gaussian blur and low resolution. Both conditions suppress fine spatial detail, which can make their feature representations similar.

## 3. Routing-Gap Analysis

![Routing-gap analysis](routing-gap-analysis.png)

Oracle routing uses the true degradation condition to select an expert. Predicted routing uses the Stage-1 router's output.

Overall accuracy was:

- Routed: **98.42%**
- Oracle: **98.43%**
- Difference: **0.01 percentage points**

This very small gap indicates that the remaining routing errors had little practical effect on the final real-versus-AI-generated prediction.

The routed model exceeded the unified detector most clearly under motion blur, low resolution, and Gaussian blur. It remained slightly behind the unified model for salt-and-pepper noise.

## 4. Robustness Across Degradation Severity

![Accuracy across degradation severity](severity-sweep.png)

The severity sweep examines whether model performance remains stable as each corruption becomes stronger.

The degradation-trained systems remain comparatively stable across most tested severity levels. The clean-only model varies much more sharply, particularly under noise, blur, and resolution loss.

The routed and oracle results closely overlap, supporting the conclusion that routing errors were not the principal performance bottleneck.

## Interpretation

The experiment does not show a large general advantage from expert routing. Instead, it shows that exposure to degraded images during training was the main source of robustness.

The routed system's **0.31 percentage-point** advantage over the unified detector should therefore be treated as a small observed improvement rather than proof that hard expert routing will consistently outperform a single degradation-trained model.

## Experimental Context

- Dataset: 140K Real and Fake Faces
- Training set: 112,000 images
- Validation set: 14,000 images
- Test set: 14,000 images
- Backbone: pretrained ResNet-18
- Feature representation: pooled layers 1-4, 960 dimensions
- Router classes: seven degradation conditions
- Expert classifiers: seven binary MLP heads
- Evaluation: accuracy, precision, recall, F1, ROC-AUC, learning curves, and confusion matrices

These figures report the final post-tuning experiment. Earlier baseline figures and development-stage notebooks are intentionally excluded from the public repository.
