# Results 

This folder contains the main evaluation figures and supporting outputs from the **Deepfake Detection Robustness** project.

The project evaluates whether degradation-aware routing improves deepfake detection when facial images are affected by different quality conditions.

---

## Headline Results

| System | Accuracy |
|---|---:|
| Clean-only baseline | **70.58%** |
| Final degradation router | **95.45%** |
| Unified detector | **98.11%** |
| Routed specialist system | **98.42%** |
| Oracle routing | **98.43%** |

The final routed system achieved **98.42% accuracy**, compared with **98.11%** for the unified detector.

This represents only a **0.31 percentage-point improvement**, indicating that specialist routing provided a small gain over an already strong unified model.

The oracle result of **98.43%** shows that the implemented router performed almost identically to ideal routing on the evaluated dataset.

---

## System Comparison

The evaluation compares four main configurations:

### Clean-Only Baseline

The clean-only model achieved **70.58% accuracy** when evaluated across degraded conditions.

This illustrates the robustness problem: a detector trained only on clean data can lose substantial performance when image quality changes.

### Unified Detector

The unified model was trained to handle the full range of evaluated degradation conditions within a single detector.

It achieved:

**98.11% accuracy**

This provides a strong baseline for assessing whether degradation-specific specialist models offer additional value.

### Routed Specialist System

The routed architecture first predicts the degradation condition and then sends the image to the corresponding specialist detector.

It achieved:

**98.42% accuracy**

The routed system therefore improved on the unified model by:

**+0.31 percentage points**

### Oracle Routing

Oracle routing uses the true degradation label to select the appropriate specialist detector.

It achieved:

**98.43% accuracy**

The extremely small gap between routed and oracle performance suggests that degradation-routing errors contributed very little to the final system error.

---

## Degradation Router

The final degradation router achieved:

**95.45% classification accuracy**

Its role is not to determine whether an image is real or fake.

Instead, it identifies the image-quality or degradation condition so that the sample can be passed to an appropriate specialist detector.

The system therefore follows the structure:

```text
Input face image
        ↓
Degradation router
        ↓
Predicted degradation condition
        ↓
Specialist deepfake detector
        ↓
Real / Fake prediction
```

---

## Key Finding

The main result is not simply that routing improved accuracy.

The experiment showed that a **single unified detector already handled the evaluated degradation conditions extremely well**.

Although the specialist-routing architecture achieved the best observed result, its advantage over the unified model was only:

**98.42% − 98.11% = 0.31 percentage points**

This means the additional routing complexity produced only a modest empirical benefit in this evaluation.

That distinction is important when considering whether the extra system complexity would be justified in a practical deployment.

---

## Robustness Finding

The comparison between the clean-only model and the degradation-aware systems highlights the importance of training for realistic image variation.

```text
Clean-only model
70.58%
        ↓
Degradation-aware training
        ↓
Unified detector
98.11%
        ↓
Specialist routing
98.42%
```

The largest improvement therefore came from making the detection system **degradation-aware**, rather than from the routing mechanism itself.

---

## Results Interpretation

The experiments support three main conclusions:

1. **Clean-data performance is not sufficient evidence of robustness.**  
   The clean-only detector performed substantially worse when exposed to degraded inputs.

2. **Training across degradation conditions produced a large robustness improvement.**  
   The unified detector reached 98.11% accuracy without requiring specialist routing.

3. **Specialist routing produced only a small additional gain.**  
   The routed system reached 98.42%, only 0.31 percentage points above the unified detector.

The results therefore support degradation-aware training while providing weaker evidence that a more complex specialist-routing architecture is necessary.

---

## Figures

The figures in this folder document the main evaluation stages of the project.

They include results covering:

- degradation-router performance
- clean-only baseline performance
- unified-model evaluation
- routed specialist performance
- oracle-routing comparison
- degradation-specific model behaviour
- final system comparison

Refer to the project notebook and case study for the full experimental workflow and interpretation.

---

## Limitations

These results should be interpreted within the scope of the experiment.

Key limitations include:

- evaluation is limited to the datasets and degradation conditions used in the project
- the routing advantage over the unified model is very small
- results do not establish robustness to unseen real-world manipulations or acquisition conditions
- high test accuracy does not guarantee generalisation to other deepfake-generation methods
- the experiment does not establish that the routed architecture is preferable under deployment constraints such as latency, compute or maintenance cost

The comparison therefore demonstrates experimental robustness under the evaluated conditions rather than universal deepfake-detection reliability.

---

## Key Takeaway

The strongest result from the project is that **degradation-aware training substantially improved deepfake-detection robustness**.

A unified detector achieved **98.11% accuracy**, while specialist routing increased this to **98.42%**.

Because the improvement from routing was only **0.31 percentage points**, the results suggest that the simpler unified model may provide a better complexity–performance trade-off unless specialist routing offers additional benefits under broader evaluation.
