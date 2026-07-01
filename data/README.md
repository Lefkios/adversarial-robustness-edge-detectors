# Data — Dataset Locations

All datasets stay **in place** on Drive (too large to move).  
This file is the canonical pointer. Use these paths in notebook `drive.mount` cells.

---

## 1. CARLA-GeAR Billboard Dataset (§5.2)

9 billboard scenarios (billboard01–09). Each has train/test split in YOLO format.

| Subdir | Drive path |
|--------|-----------|
| Per-scenario images+labels | `My Drive/yolo_patch/Billboard1/` through `Billboard9/` |
| Patched datasets (clean, defended) | `My Drive/Patched Datasets/` |
| All paper defence eval runs (148 subdirs) | `My Drive/paper_runs/` |
| Cross-model defence runs | `My Drive/paper_runs_all_models/` |
| CARLA-GeAR archive | `My Drive/patched.zip` (1.26 GB) |

### Key subdirs in Patched Datasets/
| Subdir | Contents |
|--------|---------|
| `Clean/` | Clean billboard images (unperturbed) |
| `patched_dataset1/`, `patched_dataset2/` | Patched billboard01 images |
| `Defended_FINAL/` | Final defended images (JPEG, Gaussian, median, bitdepth, none × bill04–09) |
| `Defended_Final_v2/` | All-3-models defended images (source for Appendix A) |
| `Defended_LGS_SELECTED/` | LGS with frozen hyperparameters (Table 6.18) |
| `Defended_SODA_FINAL/` | SODA defended images (Table A.20) |
| `Clean_640/` | Clean images at 640px resolution |

---

## 2. Isaac Sim Warehouse Dataset (§5.10)

10 object classes (barrel, box, bucket, cardbox, crate, fire extinguisher, forklift, pallet, person, traffic cone).

| Subdir | Drive path | Notes |
|--------|-----------|-------|
| Main dataset | `My Drive/IsaacSim_Dataset_Simple/` | images/, labels/, classes.txt, data.yaml, overlay/ |
| Test split | `My Drive/IsaacSim_test1/` | splits/ subdirectory |
| Patched images | `My Drive/isaac patched/` | digital overlay patched |
| Scene1 subset | `My Drive/isaac_scene1/` | scene1 images |
| SODA heatmaps | `My Drive/isaac_sim_soda_heatmaps/` | Fig 6.20 source |
| FP demo outputs | `My Drive/isaac_fp_demo_outputs/` | Fig 6.12 source grids |

---

## 3. Nova Carter Real-World Dataset (§5.16)

Captured from Nova Carter front stereo-left camera (1920×1200 → 640×640).  
Train/val: 94 images (same recording). Test: 94 images (different day).

| Subdir | Drive path | Notes |
|--------|-----------|-------|
| **Primary dataset** | `My Drive/nova_test_dataset_final/` | images/, labels/, data.yaml |
| Training/val split | `nova_test_dataset_final/images/` + `labels/` | 94 images |
| Test set (patched/clean eval) | `nova_test_dataset_final/eval_format_clean/` + `eval_format_patched/` | |
| Patched dataset | `nova_test_dataset_final/nova_patched_dataset/` | digital overlay |
| Rect patch dataset | `nova_test_dataset_final/nova_patched_dataset_rectangular_realworld/` | |
| Detection examples | `nova_test_dataset_final/clean_vs_patched_detection_examples/` | Fig 6.15 source |
| **Scene1 archive** | `My Drive/nova_scene1.zip` (1.20 GB) | full scene data + demo images |

### Class mapping (Table 5.13)
person, chair, table, sofa, cabinet, screen, robot, box, trashbin, cabinetnet

---

## Dataset Sizes
| Dataset | Approx size |
|---------|------------|
| CARLA-GeAR (Billboard1–9) | moderate (streamed via Drive) |
| patched.zip archive | 1.26 GB |
| IsaacSim_Dataset_Simple | moderate |
| nova_test_dataset_final | moderate (94+94 frames) |
| nova_scene1.zip | 1.20 GB |
