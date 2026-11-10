# EAE-Net: Polyp Segmentation on Kvasir-SEG

Automatic polyp segmentation in colonoscopy images using a U-Net with an
EfficientNet-B3 encoder and scSE attention. This repository contains the code
from my MS thesis in Information Technology at the University of Sargodha.

Finding polyps early is important for colorectal cancer screening, and
outlining them automatically can help doctors during colonoscopy.

## Models

Both notebooks use the same network, data, loss and settings. The only
difference is the edge branch.

| Notebook | Description |
|----------|-------------|
| `EAE_Net_edgefree_kvasir.ipynb` | U-Net with EfficientNet-B3 encoder and scSE attention (one output: the mask) |
| `EAE_Net_kvasir.ipynb` | Same model with an extra output channel that predicts the object boundary (Canny edge of the mask), edge loss weight 0.3 |

Common settings: about 13.2 million parameters, loss = 0.4 BCE + 0.4 Dice +
0.2 Focal Tversky (alpha 0.7, beta 0.3, gamma 0.75).

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
- Random seed: set `SEED` in the first lines of the training cell

## Results

Kvasir-SEG validation split, mean ± std over 3 seeds (42, 43, 44), final epoch:

| Model | Dice | IoU | Precision | Recall | Accuracy |
|-------|------|-----|-----------|--------|----------|
| EAE-Net (edge-free) | 0.888 ± 0.005 | 0.828 ± 0.005 | 0.918 ± 0.012 | 0.882 ± 0.017 | 0.973 ± 0.001 |
| EAE-Net (with edge) | 0.890 ± 0.003 | 0.830 ± 0.004 | 0.928 ± 0.010 | 0.878 ± 0.009 | 0.972 ± 0.001 |

The edge branch did not give a clear improvement on Kvasir-SEG in this setup.
The validation split is used for evaluation; results can vary slightly between
runs because of GPU non-determinism.

## How to run

1. Open a notebook in Google Colab and select a GPU runtime (T4).
2. Put `kvasir-seg.zip` in your Google Drive (`MyDrive/`).
3. Run the cells from top to bottom. Training takes about 20 minutes.
4. To reproduce the table, set `SEED` to 42, 43 and 44 and run each time.

## Files

- `EAE_Net_edgefree_kvasir.ipynb`: edge-free model
- `EAE_Net_kvasir.ipynb`: model with edge branch
- `requirements.txt`: Python libraries

## Author

Iqra Tanseer, MS Information Technology, University of Sargodha.
