# Adversarial Robustness of Edge-Deployed Object Detectors in Mobile Robotics

**Author:** Lefkios Mavroudi  
**Institution:** University of Cyprus — Department of Electrical and Computer Engineering  
**Supervisors:** Theocharis Theocharides, Antonis Savva  
**Degree:** BEng Electrical and Computer Engineering, 2025–2026

---

## Overview

This repository contains all code, results, and trained model pointers for my BEng thesis investigating adversarial patch attacks against lightweight object detectors deployed on mobile robotic platforms.

The core question: *can a small, printed adversarial patch fool an object detector running on an edge device, and what defences are effective without destroying detection performance?*

**Setup:** An EOT-based adversarial patch is optimised against YOLOv8n (white-box surrogate) on CARLA-GeAR simulated billboard scenes. The patch is then evaluated for transferability across nine billboard scenarios, four additional architectures, two domain shifts (Isaac Sim warehouse, Nova Carter real robot), and five defence strategies. Hardware profiling runs on an NVIDIA Jetson AGX Orin.

---

## Results Summary

### Attack effectiveness — own-patch (each scene attacked with its own patch)

| Model | Backbone | Params | mAP50 Clean | mAP50 Patched | Δ mAP50 | FP/img @0.5 Clean | FP/img @0.5 Patched |
|-------|----------|--------|-------------|----------------|---------|---------------------|----------------------|
| YOLOv8n (white-box) | CSP-C2f | 3.2 M | 80.6% | 49.2% | −31.5 pp | 0.26 | 10.71 |
| YOLOv8s (transfer) | CSP-C2f | 11.2 M | 89.0% | 63.2% | −25.8 pp | 0.21 | 0.22 |
| YOLOv5n (transfer) | CSP-Darknet | 1.9 M | 86.1% | 56.7% | −29.4 pp | 0.28 | 0.77 |
| RT-DETR-L (transfer) | ResNet-50-D | 32.0 M | 90.4% | 67.8% | −22.6 pp | 0.43 | 0.98 |
| NanoDet-Plus (transfer) | ShuffleNetV2 | 1.8 M | ~68% | ~61% | ~−7 pp | — | — |

Full per-scenario breakdown: [`results/tables/adversarial_robustness_final.xlsx`](results/tables/adversarial_robustness_final.xlsx)

### Cross-scenario transfer — Patch1 (trained on billboard01 only)

Patch1 is applied unchanged to billboard02–09 for each model.

| Model | mAP50 Clean (avg) | mAP50 Patched (avg) | Δ |
|-------|-------------------|----------------------|---|
| YOLOv8n | 86.5% | 52.8% | −33.7 pp |
| YOLOv8s | 90.3% | 69.8% | −20.5 pp |
| YOLOv5n | 86.6% | 62.4% | −24.2 pp |
| RT-DETR-L | 90.6% | 74.0% | −16.6 pp |
| NanoDet-Plus | 67.5% | 61.2% | −6.3 pp |

YOLOv8n is most vulnerable to transfer: billboard04 drops from 90.3% to 24.4% (−65.9 pp).

### Defence evaluation — held-out billboard04–09, YOLOv8n

| Defence | Params | mAP50 Clean | mAP50 Patched | Δ mAP50 | FP/img @0.5 (patched) |
|---------|--------|-------------|----------------|---------|------------------------|
| No defence | — | 77.8% | 58.6% | −19.2 pp | 1.59 |
| Gaussian blur | σ=4.0 | 71.1% | 40.3% | −30.8 pp | 0.23 |
| Median filter | k=13 | 71.8% | 44.1% | −27.7 pp | 0.12 |
| JPEG compression | q=95 | 77.6% | 58.6% | −19.0 pp | 1.57 |
| Bit-depth reduction | b=7 | 77.9% | 58.6% | −19.3 pp | 1.62 |
| **LGS** | σ=1.5 | **71.2%** | **60.3% (+1.6 pp)** | **−6.6 pp** | **0.16** |

Full defence results (all models, all scenarios): [`results/tables/defense_evaluation_v3.xlsx`](results/tables/defense_evaluation_v3.xlsx)

**LGS reduces FP/img at τ=0.001 by 37.86 on YOLOv8n** while slightly improving patched mAP50 (58.6% → 60.3%).  
Gaussian and median blur hurt clean accuracy significantly. JPEG and bit-depth reduction have essentially no effect at the parameters tested.

### Domain generalisation

| Setting | mAP50 Clean | mAP50 Patched | Δ |
|---------|-------------|----------------|---|
| Isaac Sim warehouse (digital overlay, YOLOv8n) | 87.5% | ~75% | ~−12 pp |
| Nova Carter real robot (digital overlay, YOLOv8n) | 78.4% | 62.4% | −16.0 pp |

### Hardware (Jetson AGX Orin, MODE 15W, TensorRT FP16)

Adversarial patches are hardware-invisible: **44/45 model–scenario pairs show no statistically significant change** in inference latency, power draw, or memory use under patched vs clean input.

SODA preprocessing latency: **31.9 ms** on Tesla T4 (Google Colab).

---

## Repository Structure

```
notebooks/
  0_data_prep/        CARLA-GeAR annotation conversion, Isaac Sim → YOLO format
  1_training/         Fine-tuning all five detectors
  2_attacks/          EOT patch optimisation — billboard, Isaac Sim, Nova Carter
  3_eval/             Cross-scenario and cross-model evaluation pipelines
  4_defence/          Defence evaluation — Gaussian/median/JPEG/bitdepth/LGS/PatchBlock/SODA
  5_analysis/         Thesis figures, presentation plots, FP demo grids

results/
  figures/            fig1–fig8 PNGs + transfer_fp PDFs (thesis figures)
  tables/             Canonical CSVs and XLSX (Tables 6.3–6.25 + Appendix A)
  logs/               Raw JSON eval logs per model × scenario × defence
  media/patches/      Adversarial patch visualisations (bill1.png, etc.)

weights/README.md     Pointers to all fine-tuned checkpoints (stay on Drive)
patches/README.md     Pointers to adversarial patch tensors (stay on Drive)
data/README.md        Pointers to all three datasets (stay on Drive)
```

---

## Datasets

| Dataset | Scenarios | Source | Notes |
|---------|-----------|--------|-------|
| **CARLA-GeAR billboard** | 9 billboard scenes (bb01–bb09) | Simulated (CARLA) | Billboard placement, varying lighting/weather |
| **Isaac Sim warehouse** | 10-class indoor scenes | Simulated (Isaac Sim) | barrel, box, bucket, crate, forklift, pallet, person, traffic cone… |
| **Nova Carter real-world** | Front stereo-left camera | Real robot (Nova Carter) | 94 train + 94 test frames, 1920×1200 → 640×640 |

LOSO-CV protocol: billboard01–03 = tuning split, billboard04–09 = held-out evaluation.

Dataset locations on Drive: see [`data/README.md`](data/README.md).

---

## Models

All models fine-tuned from COCO-pretrained weights on CARLA-GeAR (200 epochs, Tesla T4, Ultralytics framework). NanoDet-Plus uses the official NanoDet framework.

| Model | Role | Params | Drive path |
|-------|------|--------|-----------|
| YOLOv8n | White-box surrogate for patch training | 3.2 M | `yolo_trained_models/2dod_checkpoints/best.pt` |
| YOLOv8s | Transfer target | 11.2 M | `yolov8s_carlagear/train2/weights/best.pt` |
| YOLOv5n | Transfer target | 1.9 M | `yolov5n_carlagear/train/weights/best.pt` |
| NanoDet-Plus | Cross-family transfer | 1.8 M | `nanodet_carlagear/model_best/nanodet_model_best.pth` |
| RT-DETR-L | Transformer-based transfer | 32.0 M | `runs/rtdetr_carlagear2/weights/best.pt` |
| YOLOv8n (Isaac) | Domain-specific | 3.2 M | `models/isaac_clean_split_best.pt` |
| YOLOv8n (Nova) | Domain-specific | 3.2 M | `nova_test_dataset_final/yolov8n_nova_office_best.pt` |

See [`weights/README.md`](weights/README.md) for full details.

---

## Adversarial Patches

Patches are 80×80 RGB float32 tensors, optimised using EOT (Expectation over Transformation) with Adam (lr=0.03, 250 iterations, 3 seeds). Best patch = lowest training loss across seeds.

**Patch1** — trained on billboard01, used for all cross-scenario and cross-model transfer experiments:  
`yolo_patch/Billboard1/patches/best_bill1.pt`

Per-billboard patches and Nova Carter patches: see [`patches/README.md`](patches/README.md).

---

## Reproducing the Results

All notebooks run on **Google Colab** (Tesla T4 GPU) with Drive mounted.

### 1. Setup

```python
from google.colab import drive
drive.mount('/content/drive')
!pip install -r /content/drive/MyDrive/thesis-adversarial-robustness/requirements.txt
```

For NanoDet-Plus:
```bash
git clone https://github.com/RangiLyu/nanodet.git
cd nanodet && pip install -e . --no-deps
pip install pytorch-lightning==1.9.5
```

### 2. Train detectors

```
notebooks/1_training/yolo_multifolder_training.ipynb          YOLOv8n on CARLA-GeAR
notebooks/1_training/yolov8s-Yolov5n-NanoDetPlus(Training).ipynb
notebooks/1_training/rtdetr_training_eval.ipynb
notebooks/1_training/yolov8n_IsaacSim.ipynb
```

### 3. Generate adversarial patches

```
notebooks/2_attacks/Yolo_Patch_Opt.ipynb          Primary billboard attack (Patch1)
notebooks/2_attacks/Isaac_patch.ipynb              Isaac Sim digital overlay patch
notebooks/2_attacks/nova_realworld_patch_2nd.ipynb Nova Carter patch
```

### 4. Evaluate

```
notebooks/3_eval/unified_evaluation_ver2.ipynb     Tables 6.3–6.9 (CARLA-GeAR, all models)
notebooks/3_eval/nova_eval.ipynb                   Tables 6.10–6.15 (Nova Carter)
```

### 5. Defence

```
notebooks/4_defence/Defense_Evaluation_Final.ipynb     Gaussian/median/JPEG/bitdepth (Tables 6.16–6.17)
notebooks/4_defence/defense_held_out_eval.ipynb        Held-out defence results (Tables 6.23–6.25)
notebooks/4_defence/soda_thesis_figures.ipynb          SODA latency + Fig 6.20
notebooks/4_defence/Patch_Defense.ipynb                PatchBlock chunk defence (Tables 6.19–6.20)
```

### 6. Analysis and figures

```
notebooks/5_analysis/Thesis_Plots.ipynb               fig1–fig8 (thesis figures)
notebooks/5_analysis/thesis_defense_analysis.ipynb    Deep dive for viva
```

---

## Key Findings

1. **Patches are effective and transfer.** A 80×80 patch trained on one billboard scene drops YOLOv8n mAP50 by up to 65.9 pp on unseen scenes and transfers across all tested architectures, including the transformer-based RT-DETR-L.

2. **False positive flooding is the dominant threat mechanism.** Under attack, YOLOv8n FP/img at τ=0.5 increases from 0.26 to 10.71 (~41×). This matters more for robotics than mAP — the perception stack becomes flooded with phantom detections that trigger spurious downstream reactions.

3. **LGS is the most effective defence.** It recovers patched mAP50 from 58.6% to 60.3% (+1.6 pp above the undefended baseline) and cuts FP/img at τ=0.001 by 37.86 on YOLOv8n, with only a 6.6 pp clean-accuracy cost. JPEG and bit-depth reduction have essentially no effect. Gaussian and median blur reduce FPs but destroy clean detection quality.

4. **Patches are hardware-invisible.** 44/45 model–scenario pairs on the Jetson AGX Orin show no significant change in latency, power, or memory — the hardware cannot detect the attack.

5. **Domain generalisation is partial.** The patch degrades Nova Carter detection by 16 pp (digital overlay). Real-world physical placement effects remain an open question.

---

## Citation

If you use this code or results, please cite:

```bibtex
@thesis{mavroudi2026adversarial,
  author  = {Lefkios Mavroudi},
  title   = {Adversarial Robustness of Edge-Deployed Object Detectors in Mobile Robotics},
  school  = {University of Cyprus},
  year    = {2026},
  type    = {BEng Thesis}
}
```

---

## Dependencies

See [`requirements.txt`](requirements.txt). Core: `ultralytics`, `torch`, `torchvision`, `kornia`, `opencv-python`, `numpy`, `pandas`.
