# Robust Detection of AI-Generated Faces Under Image Degradation

A degradation-aware deep-learning pipeline for classifying real and AI-generated faces under common image corruptions. The project compares a clean-only detector, a unified degradation-trained model, and a two-stage routed system with seven specialist classifier heads.

> This repository presents a collaborative MSc group project. The notebook and reported results are shared as the team's work; they should not be interpreted as evidence that any one contributor individually authored every component.

## Overview

AI-generated-face detectors can perform well on clean benchmark images while becoming unreliable after compression, blur, noise, or resolution loss. This project tests whether degradation-aware training and specialist routing improve robustness across seven input conditions:

- Clean images
- Salt-and-pepper noise
- Gaussian noise
- Gaussian blur
- Motion blur
- Low resolution
- JPEG compression

The final experiment uses the **140K Real and Fake Faces** dataset with a reproducible, class-balanced **80:10:10 split**: 112,000 training images, 14,000 validation images, and 14,000 test images.

## Approach

The final pipeline uses a pretrained ResNet-18 backbone and combines features from layers 1-4 into a 960-dimensional representation. A Stage-1 router predicts the degradation condition, then selects one of seven binary expert heads for real-versus-fake classification.

Four systems are evaluated on the same test set:

| System | Purpose |
|---|---|
| Clean-only | Measures how a detector trained only on clean images generalises to degraded inputs |
| Unified | Uses one ResNet-18 detector trained across all degradation conditions |
| Routed | Uses the predicted degradation class to select a specialist expert |
| Oracle | Uses the true degradation label to select an expert, providing an upper-bound routing reference |

## Results

| System | Overall test accuracy |
|---|---:|
| Clean-only | 70.58% |
| Unified | 98.11% |
| Routed | **98.42%** |
| Oracle | 98.43% |

The degradation router achieved **95.45%** test accuracy. Routed and oracle performance differed by only 0.01 percentage points, suggesting that the remaining routing mistakes had little practical effect on final real/fake classification. The routed model exceeded the unified model by 0.31 percentage points overall; the much larger gain came from training on degraded data rather than from routing alone.

### Accuracy by degradation condition

![Post-tuning end-to-end accuracy by degradation condition](results/accuracy_by_degradation_condition.png)

### Router training and confusion matrix

![Degradation-router training curve and normalized confusion matrix](results/router_training_and_confusion_matrix.png)

The main remaining router confusion is between Gaussian blur and low resolution, which both suppress fine spatial detail.

### Severity sweep

![Post-tuning severity sweep](results/severity_sweep.png)

The unified, routed, and oracle systems remain comparatively stable across most tested severity levels. The routed and oracle curves closely overlap, reinforcing the finding that routing errors are not the main performance bottleneck.

## Technical Highlights

- PyTorch end-to-end training and evaluation pipeline
- Pretrained ResNet-18 backbone with multi-scale feature extraction
- Deterministic degradation assignment with recorded classes, severity parameters, and random seeds
- Seven-class degradation router and seven specialist MLP heads
- Clean-only, unified, routed, and oracle comparisons
- Accuracy, precision, recall, F1, ROC-AUC, learning curves, and confusion-matrix evaluation
- Fixed random seeds and an explicit train/validation/test split

## Repository Structure

```text
robust-ai-face-detection/
├── README.md
├── deepfake_detection_final.ipynb
└── results/
    ├── accuracy_by_degradation_condition.png
    ├── router_training_and_confusion_matrix.png
    └── severity_sweep.png
```

The repository intentionally contains only the final executed notebook and selected result figures. Earlier baseline and backbone-development notebooks are excluded because they do not represent the final reported implementation. The MSc report is also intentionally excluded.

## Running the Notebook

The notebook was developed for a GPU-enabled Google Colab environment. It downloads the dataset through `kagglehub`, trains several image and feature-based models, and can take substantial time and GPU memory to rerun from start to finish.

Install the principal dependencies if running in a fresh environment:

```bash
pip install kagglehub opencv-python numpy pandas matplotlib pillow \
  torch torchvision scikit-learn
```

Then open `deepfake_detection_final.ipynb` in Jupyter or Google Colab and run the cells in order. Internet access is required for the dataset download and pretrained backbone weights.

## Notes and Limitations

- The evaluation uses one dataset and synthetically applied degradations; performance should not be assumed to transfer unchanged to unseen generators, real-world image pipelines, or adversarial inputs.
- The small routed-versus-unified gain is an observed result, not evidence that routing will always outperform a single degradation-trained detector.
- The dataset is downloaded at runtime and is not redistributed in this repository. Review the source dataset's terms before reuse.
- Executed outputs are retained so the recorded experiment can be reviewed on GitHub without retraining every model.

## Acknowledgement

This work was completed collaboratively as part of an MSc group project. The public repository is intended to document the shared technical approach and results without assigning unverified component-level authorship to an individual contributor.
