# Case Study: Robust AI-Generated Face Detection Under Image Degradation

[← Back to Project Overview](https://github.com/sarahnish/deepfake-detection)

## Why This Project?

AI-generated faces are becoming increasingly realistic, creating risks such as impersonation, fraud and deceptive content. At the same time, detectors that perform well on clean images can lose accuracy after common transformations such as resizing, recompression, blur and noise.

This project therefore asks whether **degradation-aware detection can remain reliable under these real-world image changes**.

This project therefore focused on a different question:

> **Can AI-generated face detection remain reliable when image quality changes?**

I investigated whether a degradation-aware system could first identify the image condition and then route the image to a specialist classifier trained for that condition.

The project compared this approach against simpler alternatives to determine whether the additional routing complexity actually added value.

---

## Experimental Design

The experiments used the **140K Real and Fake Faces** dataset containing authentic and StyleGAN-generated faces.

Seven image conditions were evaluated:

**Clean · Salt-and-pepper noise · Gaussian noise · Gaussian blur · Motion blur · Low resolution · JPEG compression**

The final dataset used:

| Split | Images |
|---|---:|
| Training | **112,000** |
| Validation | **14,000** |
| Test | **14,000** |

The degradation pipeline recorded the degradation type, severity parameter, motion-blur angle and random seed so that the generated data could be reproduced consistently.

Rather than evaluating only one model, four systems were compared:

- **Clean-only:** trained only on clean images
- **Unified:** one detector trained across every degradation condition
- **Routed:** predicted degradation selects a specialist classifier
- **Oracle:** true degradation selects the specialist, representing perfect routing

The oracle comparison was particularly useful because it allowed us to separate **routing errors** from limitations in the expert classifiers themselves.

---

## From Baseline to Final System

The project was developed in two stages.

The initial baseline used a smaller documented dataset of **8,988 training, 1,400 validation and 2,800 test images**. Multi-scale features from ResNet-18 layers 2–4 produced an **896-dimensional representation**.

The final post-tuning experiment increased the dataset substantially and expanded the feature representation.

| | Baseline | Final |
|---|---:|---:|
| Training images | 8,988 | **112,000** |
| Feature layers | ResNet 2–4 | **ResNet 1–4** |
| Feature size | 896 | **960** |
| Router accuracy | 82.36% | **95.45%** |

Adding features from the earlier ResNet layer provided additional low-level information such as textures, edges and noise patterns that could help distinguish degradation types.

ResNet-18 was retained as the final backbone after comparison with EfficientNet-B0. The selection considered validation performance and ROC-AUC rather than relying on one accuracy result alone.

---

## Final Architecture

The final model used a **two-stage hard-routing mixture-of-experts architecture**.

```text

Input Image
    ↓
ResNet-18
    ↓
Multi-Scale Features
Layers 1–4 → 960 dimensions
    ↓
Degradation Router
    ↓
Predicted Image Condition
    ↓
Hard Routing
    ↓
One of 7 Expert MLPs
    ↓
Real / AI-Generated

```

The ResNet-18 backbone creates a shared feature representation.

The Stage-1 router predicts one of the seven degradation classes, and that prediction selects the corresponding binary expert classifier.

The expert then determines whether the face is **real or AI-generated**.

This design keeps the specialist classifiers relatively lightweight because the expensive image-feature extraction is shared.

---

## What Did We Find?

The final results were:

| System | Test Accuracy |
|---|---:|
| Clean-only | **70.58%** |
| Unified | **98.11%** |
| Routed | **98.42%** |
| Oracle | **98.43%** |

The result that mattered most was not simply the final **98.42% accuracy**.

### Degradation-aware training mattered most

Moving from the clean-only model to the unified degradation-trained model produced:

> **70.58% → 98.11% = +27.53 percentage points**

This demonstrated that exposing the detector to degraded images during training was the main source of robustness.

### Routing helped, but only slightly

Moving from the unified detector to the routed system produced:

> **98.11% → 98.42% = +0.31 percentage points**

The routed system performed best among the practical models, but the improvement was much smaller than the gain from degradation-aware training.

### The router was not the bottleneck

The Stage-1 degradation router achieved **95.45% accuracy**.

However:

> **Routed: 98.42%**  
> **Oracle: 98.43%**

Perfect routing improved final accuracy by only **0.01 percentage points**.

This showed that remaining routing mistakes had almost no effect on the final prediction.

Further improvement would therefore be more likely to come from the shared feature representation or stronger expert specialisation than from simply making the router more accurate.

---

## Error & Robustness Analysis

The main degradation-classification confusion occurred between:

> **Gaussian blur ↔ Low resolution**

Both remove fine spatial information, making their feature representations more difficult to distinguish.

The severity experiments also showed that the **unified, routed and oracle models remained comparatively stable as degradation strength increased**.

The clean-only detector was much more sensitive.

For example, its accuracy fell to **52.50% under salt-and-pepper noise**, while remaining at **84.20% under JPEG compression**.

This reinforced the idea that evaluating only clean-image accuracy gives an incomplete picture of detector robustness.

---

## Limitations

The project also has several important limitations.

**Generator coverage.** The dataset primarily contains StyleGAN-generated faces, so the results should not automatically be generalised to diffusion models or other synthesis methods.

**Controlled degradations.** Each degradation was applied individually. Real images may instead experience combinations such as resizing, compression and sharpening in sequence.

**Shared representation.** All seven expert classifiers rely on the same frozen ResNet-18 features. This may limit how strongly the experts can specialise.

**Small routing gain.** The routed model's **+0.31 percentage-point** advantage over the unified model was not statistically significance-tested, so it should be treated as an observed improvement rather than confirmed evidence of superiority.

---

## What I Would Explore Next

A stronger follow-up study could:

- test images processed through real social-media and messaging platforms
- introduce **combined degradations**
- repeat experiments across multiple seeds and statistically test the routed-versus-unified difference

These experiments would help determine whether expert specialisation becomes more valuable under more diverse and realistic conditions.

---

## What I Learned

The main lesson from the project was that **more architecture does not automatically mean a meaningfully better system**.

The biggest improvement came from improving the training conditions and exposing the model to realistic degradation, rather than from adding the expert-routing mechanism.

The oracle comparison also showed the value of designing experiments that explain **why** a system behaves as it does rather than reporting only a final accuracy.

---

## Takeaway

The project began by asking whether degradation-specific expert routing could make AI-generated face detection more robust.

The final result was more nuanced:

> **Degradation-aware training was essential. Expert routing provided a smaller additional benefit.**

The unified degradation-trained detector already achieved **98.11%**, while routing increased this to **98.42%**.

This made the most useful outcome of the project not simply a high accuracy score, but a clearer understanding of **where the robustness improvement actually came from**.
