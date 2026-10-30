# EAE-Net: Polyp Segmentation

Automatic polyp segmentation in colonoscopy images using a U-Net with an
EfficientNet-B3 encoder and scSE attention. This repository contains the code
from my MS thesis in Information Technology at the University of Sargodha.

Finding polyps early is important for colorectal cancer screening, and
outlining them automatically can help doctors during colonoscopy.

This repository has two versions of the model: an edge-free version and an
edge-aware version.

## Model

| Part | Details |
|------|---------|
| Architecture | U-Net (`segmentation_models_pytorch`) |
| Encoder | EfficientNet-B3, ImageNet pretrained |
| Attention | scSE blocks in the decoder |
| Parameters (edge-free) | about 13.2 million |
| Loss | 0.4 BCE + 0.4 Dice + 0.2 Focal Tversky (alpha 0.7, beta 0.3, gamma 0.75) |

## Dataset

[Kvasir-SEG](https://datasets.simula.no/kvasir-seg/): 1000 polyp images with
masks. The data is not included here. Download it and keep the zip file in
Google Drive as `kvasir-seg.zip`.

## Training setup

- Input size: 256 x 256
- Split: 80% train, 20% validation (`random_state=42`)
- Optimizer: AdamW, learning rate 1e-4, weight decay 1e-5
- Scheduler: cosine annealing with warm restarts (T_0 = 5, T_mult = 2)
- Epochs: 25, batch size 8, mixed precision
- Augmentation: flips, brightness/contrast, hue/saturation, elastic transform, CLAHE
- Random seed: 42

## Results

Edge-free model, Kvasir-SEG validation split, single run with seed 42:

| Dice | IoU | Precision | Recall | Accuracy |
|------|-----|-----------|--------|----------|
| 0.885 | 0.825 | 0.916 | 0.879 | 0.971 |

## How to run

1. Open a notebook in Google Colab and select a GPU runtime (T4).
2. Put `kvasir-seg.zip` in your Google Drive (`MyDrive/`).
3. Run the cells from top to bottom. Training takes about 20 minutes.

The notebook trains the model, saves the weights, prints the metrics and
shows example predictions (original image, ground truth, prediction).

## Files

- `eae_net_edgefree_kvasir.ipynb`: edge-free version (data, model, training, evaluation, visual results)
- `eae_net_edge_kvasir.ipynb`: edge-aware version
- `requirements.txt`: Python libraries

## Author

Iqra Tanseer, MS Information Technology, University of Sargodha.
