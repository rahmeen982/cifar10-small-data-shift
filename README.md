# cifar10-small-data-shift
# CIFAR-10 with 500 Training Images under Distribution Shift

A small CNN with Inception modules (142,970 parameters, trained from scratch) is trained on only 400 CIFAR-10 images
(+100 for validation) and tested on a shifted test set (rotation, brightness/contrast, blur, noise, occlusion and a hidden corruption).
The notebook covers Parts A–F: baseline, augmentation design, regularization, ablation study, error analysis and test-time augmentation.

## Files
- `cifar10_small_data_shift.ipynb`: the full, executed notebook (all outputs visible)
- `requirements.txt`: Python packages
- `summary.docx`: one-page summary

## How to reproduce the results
**Option 1: Google Colab (recommended)**
1. Open the notebook in Colab: https://colab.research.google.com/github/<username>/<repo>/blob/main/cifar10_small_data_shift.ipynb
2. `Runtime` → `Change runtime type` → select a **GPU** → `Save`.
3. `Runtime` → `Run all`. CIFAR-10 is downloaded automatically through `torchvision`, so no data needs to be added by hand. A full run takes roughly 10–30 minutes.

**Option 2: on your own computer**
```bash
git clone https://github.com/rahmeen982/cifar10-small-data-shift.git
cd cifar10-small-data-shift
pip install -r requirements.txt
jupyter notebook cifar10_small_data_shift.ipynb   # then Run All
```

## Seeds and splits
- `train_small`: 50 images per class chosen with `np.random.RandomState(1234)` from the 50,000 training images; split per class into 40 train / 10 validation (400 / 100).
- `clean_test`: the original 10,000 test images. `shifted_test`: `make_shifted(clean_test, seed=2024)`.
- Hyper-parameter search seed 99 (validation split only); ablation seeds 0, 1, 2; final model seeds 10, 11, 12. All results are mean ± std over 3 seeds, at most 60 epochs.
- The test sets are never used for training, validation or tuning.
- The hidden corruption in the notebook is a placeholder (random colour-channel swap) in the function `hidden_corruption`.

## Results (mean ± std, 3 seeds)
| Configuration | Val acc | Clean test | Shifted test |
|---|---|---|---|
| No augmentation | 43.00 ± 1.00 | 40.61 ± 3.85 | 16.60 ± 0.38 |
| Geometric only | 39.67 ± 2.31 | 39.31 ± 1.43 | 16.92 ± 0.62 |
| Photometric only | 44.00 ± 6.08 | 41.05 ± 1.50 | 19.09 ± 1.08 |
| Geometric + photometric | 42.67 ± 2.52 | 40.26 ± 3.04 | 20.31 ± 0.32 |
| + Cutout / Random Erasing | 42.00 ± 1.00 | 38.13 ± 0.56 | 21.17 ± 0.26 |
| + Mixup/CutMix + label smoothing | 40.67 ± 3.21 | 38.64 ± 0.52 | 21.62 ± 0.62 |
| Final model (Photometric only) | 42.67 ± 2.31 | 40.94 ± 1.97 | 18.17 ± 1.16 |

Final model selected by validation accuracy. Largest single gain: photometric augmentation (+2.49 points over the baseline).
