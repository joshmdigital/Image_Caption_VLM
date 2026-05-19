# Image Captioning with CNN + LSTM
Development of image captioning, large language model pipeline via consumer hardware with integrations of python into Rstudio.

[![Code License: Apache 2.0](https://img.shields.io/badge/Code%20License-Apache%202.0-blue.svg)](LICENSE)
[![Model License: CC BY-NC 4.0](https://img.shields.io/badge/Model%20License-CC%20BY--NC%204.0-lightgrey.svg)](weights/MODEL_WEIGHTS_LICENSE.md)
[![R 4.4.2](https://img.shields.io/badge/R-4.4.2-276DC3.svg)](https://www.r-project.org/)
[![Python 3.10](https://img.shields.io/badge/Python-3.10-3776AB.svg)](https://www.python.org/)
[![TensorFlow 2.11](https://img.shields.io/badge/TensorFlow-2.11.0-FF6F00.svg)](https://www.tensorflow.org/)

An end-to-end image captioning system that pairs a **MobileNetV2** convolutional encoder with an **LSTM decoder** and **Luong-style attention**, trained on the Flickr8k dataset. The project was built entirely on a consumer laptop using R and Python together via the `reticulate` package, demonstrating that modern deep learning workflows can be developed and refined without dedicated GPU infrastructure.

> **STAT 702 Final Project** · San Diego State University · Joshua Moffat · May 2025

---

## Project Overview

At the intersection of computer vision and natural language processing, image captioning trains a model to look at a photograph and generate a sentence describing it. Applications include alt-text generation for accessibility, descriptive search for image archives, and assistive tools for visually impaired users.

This project implements a classic encoder–decoder architecture: a pretrained CNN extracts visual features, a 1×1 convolution preserves spatial structure for attention, and an LSTM decoder generates a caption one word at a time while dynamically attending to relevant image regions. The model was developed iteratively — starting with a coarse hyperparameter grid search on a small subset of data, then refined on the full dataset with two rounds of layer-by-layer fine-tuning of the MobileNetV2 backbone.

The broader theme: as consumer hardware improves and generative-AI assistants make deep-learning tooling more approachable, complete model-development workflows like this one are increasingly within reach of students and self-taught practitioners working on personal machines.

## Results

| Metric | Value |
| --- | --- |
| Final validation SPICE | **0.104** |
| Best hyperparameters | embed_dim=128, lstm_units=512, lr=1e-3 |
| Training set | 6,000 images × 5 captions = 30,000 pairs |
| Validation set | 1,000 images |
| Vocabulary size (≥5 occurrences) | 2,478 words |
| Max caption length (95th percentile) | 20 tokens |

SPICE (Semantic Propositional Image Caption Evaluation) was chosen over BLEU/METEOR because it parses captions into scene graphs of objects, attributes, and relations — better aligned with what humans judge as "a good description."

### Example Generated Captions

Captions below were produced by the final model using beam search (k=3) on held-out validation images. Source images are from Flickr8k and are not redistributed here.

| Predicted Caption | Ground Truth |
| --- | --- |
| a man and a woman sitting on a sidewalk | Two women, one sitting and one standing, near a tree |
| there is a boy in a white shirt | A little boy smiles and points while a man looks in the direction of the child's gaze |
| a man surfing in the ocean | A surfer surfs a wave |
| two dogs play in the snow | Two dogs play in the snow |

The model handles object-centric, unambiguous scenes well (perfect match on the dogs/snow example) and frequently captures the core activity (surfing) with semantic flexibility. It struggles more with person-counting, gender inference, and relational details — see `ImageCaptionFinal.pdf` for full discussion.

## Architecture

```
Input image (224×224×3)
        │
        ▼
  MobileNetV2 (ImageNet weights, last 10 layers fine-tuned)
        │
        ├──► Average pool ──► Dense(embed_dim) ──► initial LSTM state
        │
        └──► Spatial features ──► 1×1 Conv ──► Reshape ──► (B, regions, embed_dim)
                                                                │
                                                                ▼
                                                       Luong-style attention
                                                                ▲
                                                                │
Caption tokens ──► Embedding(mask_zero=True) ──► LSTM ──► hidden states
                                                                │
                                                                ▼
                                  Concat(context, hidden) ──► TimeDistributed Dense ──► Softmax
```

Key design choices:
- **Spatial feature preservation.** Global pooling is intentionally skipped so attention can weight different image regions per word.
- **Image-conditioned LSTM initialization.** A global pooled image vector initializes the LSTM's hidden and cell states, giving the decoder a holistic scene summary before word generation begins.
- **Masked loss.** A custom `ReMask` layer re-applies the padding mask after the attention concat so padding tokens don't contribute to the cross-entropy loss.
- **Beam search at inference.** Greedy decoding during training (for fast SPICE callbacks); beam width 3 at test time for fluency.

## Tech Stack

- **R 4.4.2** in RStudio — orchestration, file paths, diagnostic plots
- **Python 3.10** — model construction, training, evaluation
- **`reticulate`** — runs a live Python session inside R for seamless interop
- **TensorFlow 2.11.0 / Keras** — model and training pipeline
- **MobileNetV2** (Keras Applications, ImageNet-pretrained) — visual encoder
- **`pycocoevalcap`** — provides the SPICE metric
- **OpenJDK 23.0.2** — required Java runtime for SPICE
- **Miniconda** — Python environment management
- Additional Python: `numpy`, `pandas`, `pillow`, `tqdm`, `scikit-learn`, `matplotlib`, `nltk`, `spacy`, `h5py`
- Additional R: `tidyverse`, `here`, `ggplot2`, `jsonlite`

## Hardware

Developed on a personal laptop:
- MacOS 15.3.1
- 2.3 GHz 8-core Intel Core i9
- AMD Radeon Pro 5500M 4 GB + Intel UHD Graphics 630 1536 MB
- 16 GB RAM

No dedicated GPU was used. Training took several hours per fine-tuning cycle. With more compute available, full-set SPICE evaluation and additional fine-tuning would likely push the score higher.

## Repository Structure

```
Image_Caption_VLM/
├── README.md                       # this file
├── LICENSE                         # Apache 2.0, covers source code
├── NOTICE                          # required by Apache 2.0
├── THIRD_PARTY_NOTICES.md          # detailed attribution
├── ImageCaptionModel.Rmd           # main R Markdown notebook
├── ImageCaptionFinal.pdf           # full project paper
└── weights/
    ├── README.md                   # pointer to GitHub Release for .h5
    └── MODEL_WEIGHTS_LICENSE.md    # CC BY-NC 4.0 for the trained weights
```

## Setup

The pipeline runs from an R Markdown document that calls into a Python conda environment. One-time setup:

### 1. Install R, RStudio, and Miniconda

- R 4.4.2 from [r-project.org](https://www.r-project.org/)
- RStudio Desktop from [posit.co](https://posit.co/download/rstudio-desktop/)
- Miniconda from [docs.conda.io](https://docs.conda.io/en/latest/miniconda.html)

### 2. Install Java (required by SPICE)

On macOS with Homebrew:

```bash
brew install openjdk@23
```

Make sure the Java path is visible to R/RStudio (you may need to set `JAVA_HOME` in your shell profile).

### 3. Create the Python environment

The Rmd contains a commented-out setup block. Run it once to create the conda env:

```r
library(reticulate)
env <- "caption-env"
conda_create(env, packages = c("python=3.10"))
conda_install(env, c(
  "tensorflow==2.11.0", "numpy", "pillow", "tqdm",
  "scikit-learn", "matplotlib", "nltk", "pycocoevalcap", "spacy"
))
```

Then in subsequent sessions, point `reticulate` at the env's Python binary at the top of the Rmd:

```r
python_path <- "/Users/<you>/Library/r-miniconda/envs/caption-env/bin/python"
use_python(python_path, required = TRUE)
```

### 4. Get the Flickr8k dataset

See the [Dataset](#dataset) section below — Flickr8k is **not redistributed in this repository** due to its licensing terms.

### 5. (Optional) Download pretrained weights

See the [Trained Weights](#trained-weights) section to skip retraining.

### 6. Open and run the notebook

Open `ImageCaptionModel.Rmd` in RStudio and run the chunks in order.

## Dataset

This project was trained on the **Flickr8k** dataset, which is **not included** in this repository. The dataset is provided by its authors for **non-commercial research and educational use only**, and the individual photographs remain under the copyright of their original Flickr authors.

To obtain Flickr8k:

1. Download a current mirror, such as [Kaggle: Flickr8k](https://www.kaggle.com/datasets/adityajn105/flickr8k).
2. Unzip into a folder named `flickr8k/` at the project root:

   ```
   flickr8k/
   ├── images/        # 8,091 JPEG image files
   └── captions.txt   # comma-separated: image,caption
   ```

3. By using Flickr8k, you agree to its non-commercial research/educational terms.

Canonical citation:

> Hodosh, M., Young, P., & Hockenmaier, J. (2013). Framing image description as a ranking task: Data, models and evaluation metrics. *Journal of Artificial Intelligence Research*, 47, 853–899.

## Trained Weights

The trained model checkpoint (`best_captioner.h5`, ~32 MB) is available as a release asset:

**[Download from Release v1.0](https://github.com/joshmdigital/Image_Caption_VLM/releases/tag/v1.0)**

Direct link to the file:
```
https://github.com/joshmdigital/Image_Caption_VLM/releases/download/v1.0/best_captioner.h5
```

After download, place it in `weights/best_captioner.h5` and load it from the Rmd:

```python
modelrev2.load_weights("weights/best_captioner.h5")
```

The weights are licensed under **CC BY-NC 4.0** — see [weights/MODEL_WEIGHTS_LICENSE.md](weights/MODEL_WEIGHTS_LICENSE.md) for details. The non-commercial restriction reflects the upstream Flickr8k training-data terms.

## Methodology Highlights

The full methodology, hyperparameter search results, fine-tuning strategy, and model diagnostics are documented in [ImageCaptionFinal.pdf](ImageCaptionFinal.pdf). A brief summary:

1. **Two-stage hyperparameter search.** A coarse grid search on 400 training / 200 validation samples narrowed down embedding dimension, LSTM units, and learning rate. A full-data refit on the top three configurations selected the winning combination by validation SPICE rather than cross-entropy loss.

2. **Staged CNN fine-tuning.** After training the decoder with the CNN frozen, the last 20 MobileNetV2 layers were unfrozen at a learning rate of 1e-5. A second cycle unfroze only the last 10 layers at 5e-5 with `ReduceLROnPlateau` to prevent catastrophic forgetting.

3. **SPICE-guided checkpointing.** Validation cross-entropy plateaued around 2.70 while validation SPICE continued to climb from ~0.100 to 0.104 — a clean demonstration that token-level loss and caption-level semantic quality measure different things. The final checkpoint was selected at peak SPICE, not minimum loss.

4. **Beam search at inference.** Beam width 3 produced more fluent captions than greedy decoding while keeping inference fast enough for interactive use.

## License

This repository uses a **dual licensing** structure:

- **Source code** (`.R`, `.Rmd`, `.py`, scripts, documentation) — licensed under the [Apache License, Version 2.0](LICENSE).
- **Trained model weights** (`.h5` checkpoint) — licensed under [Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)](weights/MODEL_WEIGHTS_LICENSE.md). This separate, more restrictive license reflects the non-commercial research-use terms of Flickr8k.
- **Flickr8k dataset** — **NOT redistributed here**; obtain separately and abide by its terms.

For attribution of third-party components (MobileNetV2, TensorFlow/Keras, SPICE, reticulate, etc.), see [NOTICE](NOTICE) and [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

## Citation

If this project is useful to your work, please cite:

```bibtex
@misc{moffat2025imagecaption,
  author       = {Moffat, Joshua},
  title        = {Image Captioning using Convolutional Neural Networks and Long Short-Term Memory},
  year         = {2025},
  howpublished = {STAT 702 Final Project, San Diego State University},
  url          = {https://github.com/joshmdigital/Image_Caption_VLM}
}
```

## Selected References

- **MobileNetV2:** Sandler, M., Howard, A., Zhu, M., Zhmoginov, A., & Chen, L.-C. (2018). *MobileNetV2: Inverted Residuals and Linear Bottlenecks.* CVPR. [arXiv:1801.04381](https://arxiv.org/abs/1801.04381)
- **Show and Tell (CNN+LSTM captioning):** Vinyals, O., Toshev, A., Bengio, S., & Erhan, D. (2015). *Show and tell: A neural image caption generator.* CVPR. [arXiv:1411.4555](https://arxiv.org/abs/1411.4555)
- **SPICE metric:** Anderson, P., Fernando, B., Johnson, M., & Gould, S. (2016). *SPICE: Semantic Propositional Image Caption Evaluation.* ECCV. [arXiv:1607.08822](https://arxiv.org/abs/1607.08822)
- **Flickr8k:** Hodosh, M., Young, P., & Hockenmaier, J. (2013). *Framing image description as a ranking task: Data, models and evaluation metrics.* JAIR, 47, 853–899.

## Acknowledgments

Generative-AI assistants (ChatGPT 4o, o3, and o4) were used during this project to research literature, guide environment setup, develop and optimize Python code, debug TensorFlow and SPICE errors, and revise written analyses. All content was reviewed and edited by the author, who takes full responsibility for the final work.

This project would not exist without the open-source ecosystem: TensorFlow/Keras (Google), MobileNetV2 (Sandler et al.), the Flickr8k dataset (Hodosh, Young, Hockenmaier), the `reticulate` package (RStudio/Posit), and pycocoevalcap maintainers.

## Contact

Joshua Moffat · joshua.moffat@gmail.com
