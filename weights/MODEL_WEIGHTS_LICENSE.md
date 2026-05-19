# License for the Trained Model Weights

The trained model weight file(s) distributed in this repository
(for example `best_captioner.h5`) are **licensed separately from the
source code** in this repository.

## License: Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)

The trained weights are released under the
[Creative Commons Attribution-NonCommercial 4.0 International License (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/).

You are free to:

- **Share** — copy and redistribute the weights in any medium or format
- **Adapt** — remix, transform, and build upon the weights

Under the following terms:

- **Attribution** — You must give appropriate credit, provide a link to
  the license, and indicate if changes were made.
- **NonCommercial** — You may not use the weights for commercial purposes.

## Why a separate, non-commercial license?

The weights were trained on the **Flickr8k dataset**, which the dataset
distributors make available *for non-commercial research and educational
use only*. The individual photographs in Flickr8k remain under the
copyright of their original Flickr authors.

Out of respect for those terms, the weights — which encode information
learned from the Flickr8k images and captions — are released under a
license that preserves the same non-commercial spirit.

## Components of the weight file

The `.h5` checkpoint contains weight values for two distinct
sub-components:

1. **MobileNetV2 backbone (fine-tuned).** The convolutional encoder was
   initialized from MobileNetV2's ImageNet-pretrained weights, which are
   licensed by The TensorFlow Authors under the Apache License, Version
   2.0. The final 10 convolutional layers were fine-tuned on Flickr8k in
   this project. The Apache 2.0 attribution requirements for the
   MobileNetV2 contribution are satisfied by the [NOTICE](NOTICE) file
   and [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) included in this
   repository.

2. **Caption decoder (LSTM + embedding + attention + dense layers).**
   Trained from scratch by Joshua Moffat on the Flickr8k dataset.

## Attribution suggestion

If you use these weights in your work, please cite:

```
Moffat, J. (2025). Image Captioning using Convolutional Neural Networks
and Long Short-Term Memory. STAT 702 Final Project, San Diego State
University.
```

And acknowledge the upstream sources:

- MobileNetV2 (Sandler et al., 2018; The TensorFlow Authors)
- Flickr8k (Hodosh, Young, & Hockenmaier, 2013)

## Disclaimer

The weights are provided "AS IS", without warranty of any kind. The
author makes no representations or warranties regarding the accuracy,
completeness, or suitability of the weights for any purpose. Generated
captions may be inaccurate, biased, or otherwise unsuitable for any
particular use case.
