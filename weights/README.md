# Weights — Fine-Tuned Detector Checkpoints

All weights stay **in place** on Drive. This file is the canonical pointer.  
Do NOT move these files — they are too large and referenced by absolute paths in notebooks.

## CARLA-GeAR Models (thesis §5.5)

| Model | Drive path | Size | Epochs |
|-------|-----------|------|--------|
| **YOLOv8n** (white-box surrogate) | `My Drive/yolo_trained_models/2dod_checkpoints/best.pt` | 6.3 MB | 200 |
| **YOLOv8s** (transfer target) | `My Drive/yolov8s_carlagear/train2/weights/best.pt` | 21.6 MB | 200 |
| **YOLOv5n** (transfer target) | `My Drive/yolov5n_carlagear/train/weights/best.pt` | 5.3 MB | 200 |
| **NanoDet-Plus** (cross-family) | `My Drive/nanodet_carlagear/model_best/nanodet_model_best.pth` | 13.2 MB | 200 |
| **RT-DETR-L** (transformer ref) | `My Drive/runs/rtdetr_carlagear2/weights/best.pt` | 189 MB | ~140 |

## Domain-Specific Models

| Model | Drive path | Size | Dataset |
|-------|-----------|------|---------|
| **YOLOv8n Isaac Sim** | `My Drive/models/isaac_clean_split_best.pt` | 6.0 MB | Isaac Sim warehouse |
| **YOLOv8n Nova Carter** | `My Drive/nova_test_dataset_final/yolov8n_nova_office_best.pt` | 6.0 MB | Nova Carter office |

## Training Config (all models, §5.6)
- Framework: Ultralytics (YOLO/RT-DETR), NanoDet framework for NanoDet-Plus
- Initialisation: COCO-pretrained weights
- Epochs: 200, GPU: Tesla T4 (Google Colab)
- Checkpoint: best validation mAP checkpoint
