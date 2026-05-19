# Trained Model Weights

The trained model checkpoint (`best_captioner.h5`, ~32 MB) is **not stored in this folder**. It is hosted as a GitHub Release asset to keep repository clones lightweight.

## Download

**Release page:** [v1.0](https://github.com/joshmdigital/Image_Caption_VLM/releases/tag/v1.0)

**Direct download:**
```
https://github.com/joshmdigital/Image_Caption_VLM/releases/download/v1.0/best_captioner.h5
```

Or from the command line:

```bash
curl -L -o best_captioner.h5 \
  https://github.com/joshmdigital/Image_Caption_VLM/releases/download/v1.0/best_captioner.h5
```

## Installation

After downloading, place `best_captioner.h5` directly inside this `weights/` folder:

```
weights/
├── README.md                       # this file
├── MODEL_WEIGHTS_LICENSE.md        # license terms for the weights
└── best_captioner.h5               # ← place the downloaded file here
```

Then load it from the Rmd:

```python
modelrev2.load_weights("weights/best_captioner.h5")
```

## About the Weights

- **Architecture:** MobileNetV2 encoder (ImageNet-pretrained, last 10 layers fine-tuned) + LSTM decoder with Luong-style attention
- **Training data:** Flickr8k (6,000 training images)
- **Final validation SPICE:** 0.104
- **Hyperparameters:** embed_dim=128, lstm_units=512, lr=1e-3

See the main project [README](../README.md) and [ImageCaptionFinal.pdf](../ImageCaptionFinal.pdf) for full details.

## License

The weights are licensed under **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)** — see [MODEL_WEIGHTS_LICENSE.md](MODEL_WEIGHTS_LICENSE.md) in this folder for the full terms.

The non-commercial restriction reflects the upstream Flickr8k training-data terms; the MobileNetV2 base weights remain © The TensorFlow Authors under Apache 2.0 (see [../NOTICE](../NOTICE) and [../THIRD_PARTY_NOTICES.md](../THIRD_PARTY_NOTICES.md) at the repo root).
