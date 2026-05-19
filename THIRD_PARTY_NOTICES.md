# Third-Party Notices

This project incorporates and depends on the following third-party
materials. Each is listed with its source, license, and the role it
plays in this repository.

---

## MobileNetV2 (Architecture and ImageNet-Pretrained Weights)

- **Source:** https://github.com/tensorflow/models/tree/master/research/slim/nets/mobilenet
- **License:** Apache License, Version 2.0
- **Copyright:** The TensorFlow Authors
- **Reference:** Sandler, M., Howard, A., Zhu, M., Zhmoginov, A., & Chen,
  L.-C. (2018). *MobileNetV2: Inverted Residuals and Linear Bottlenecks.*
  CVPR 2018. arXiv:1801.04381
- **Use in this project:** Loaded via `keras.applications.MobileNetV2(weights="imagenet")`
  as the convolutional encoder. The final 10 convolutional layers were
  unfrozen and fine-tuned on Flickr8k captions during training. The fine-tuned
  weight values are included in the model checkpoint distributed with this
  repository.

A copy of the Apache License, Version 2.0 is included in the [LICENSE](LICENSE)
file at the root of this repository.

---

## Flickr8k Dataset (Training and Evaluation Data)

- **Source (original):** Distributed by Micah Hodosh, Peter Young, and
  Julia Hockenmaier (University of Illinois at Urbana-Champaign).
- **Common mirrors:** https://www.kaggle.com/datasets/adityajn105/flickr8k
- **Terms:** Non-commercial research and educational use only. The
  Flickr8k distributors do not own the copyright to the images; individual
  photographs remain under the copyright of their original Flickr authors.
- **Reference:** Hodosh, M., Young, P., & Hockenmaier, J. (2013). *Framing
  image description as a ranking task: Data, models and evaluation
  metrics.* Journal of Artificial Intelligence Research, 47, 853-899.
- **Use in this project:** Used as the training and validation dataset
  (6,000 training images / 1,000 validation / 1,000 held out). The dataset
  itself is **not redistributed** in this repository. Users wishing to
  retrain or reproduce results must download Flickr8k separately and
  agree to its terms.

---

## SPICE / pycocoevalcap (Evaluation Metric)

- **Source:** https://github.com/salaniz/pycocoevalcap (Python 3 port of
  the original `coco-caption` repository)
- **License:** See package LICENSE (BSD-style for pycocoevalcap; SPICE
  itself is licensed by its authors for research use).
- **Reference:** Anderson, P., Fernando, B., Johnson, M., & Gould, S.
  (2016). *SPICE: Semantic Propositional Image Caption Evaluation.*
  arXiv:1607.08822.
- **Use in this project:** Used during training callbacks and final
  evaluation to score generated captions against ground-truth references.
  SPICE requires a Java runtime; this project was tested with OpenJDK
  23.0.2.

---

## TensorFlow / Keras (Deep Learning Framework)

- **Source:** https://github.com/tensorflow/tensorflow
- **License:** Apache License, Version 2.0
- **Copyright:** The TensorFlow Authors / Google LLC
- **Use in this project:** Model construction (Keras functional API),
  training, optimization, and inference. This project was developed
  against TensorFlow 2.11.0.

---

## reticulate (R–Python Interoperability)

- **Source:** https://github.com/rstudio/reticulate
- **License:** Apache License, Version 2.0
- **Copyright:** RStudio, PBC and contributors
- **Use in this project:** Bridges the R session (RStudio) with a
  Miniconda-managed Python environment so that the model can be built and
  trained from within an R Markdown document.

---

## Other Python Dependencies

The Python environment also depends on: `numpy`, `pandas`, `pillow`,
`tqdm`, `scikit-learn`, `matplotlib`, `nltk`, `spacy`, and `h5py`. Each
is distributed under its own permissive open-source license (typically
BSD-3-Clause or MIT). Refer to each package's distribution for full
license text.

## Other R Dependencies

The R environment also depends on: `tidyverse`, `here`, `ggplot2`,
`jsonlite`, and `tibble`. Each is distributed under its own permissive
open-source license.
