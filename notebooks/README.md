# Notebook

The public repository should contain one executed notebook:

```text
degradation-aware-routing.ipynb
```

This is the final post-tuning implementation and should retain its stored outputs so the recorded experiment can be inspected directly on GitHub.

The earlier baseline and backbone-development notebooks should not be uploaded because they document intermediate work and do not match the final reported implementation.

## Running the Notebook

The notebook was developed for a GPU-enabled Google Colab environment. It downloads the 140K Real and Fake Faces dataset through `kagglehub`, trains multiple image and feature-based models, and can require substantial GPU time and memory.

To reproduce the experiment:

1. Open the notebook in Google Colab.
2. Select a GPU runtime.
3. Install the packages from the root `requirements.txt` if required.
4. Run the cells in order.
5. Allow the dataset and pretrained ResNet-18 weights to download.

The dataset and trained checkpoints are not included in the repository.
