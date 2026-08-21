# SRS-DETR: DETR for Small Object Detection in Remote Sensing Images

This is the official PyTorch implementation of the paper **"SRS-DETR: DETR for Small Object Detection in Remote Sensing Images"**.

> SRS-DETR is a novel DETR-variant model specifically designed for small object detection in remote sensing images. It introduces three key designs — the **Deformable Feature Correlation (DFC)** module, the **Dynamic Multi-scale Sequence Fusion (DMSF)** module, and the **GWIoU** loss function — to jointly improve detection accuracy while keeping the computational cost under control.

[![Paper](https://img.shields.io/badge/Paper-here-green.svg)]()
[![License](https://img.shields.io/badge/License-MIT-blue.svg)]()

---

## Framework

![framework](figs/framework.png)

*The overall architecture of SRS-DETR. The features {S3, S4, S5} are extracted from the backbone. S5 is first processed by the DFC module (denoted as F5), then {S3, S4, F5} are dynamically fused by the DMSF module, and finally the fused features are fed into the decoder. GWIoU is used as the box regression loss during training.*

## Abstract

In the field of remote sensing image processing, small object detection presents a significant challenge due to the diminutive size of the objects, limitations in image resolution, and the difficulty of recognizing objects within complex backgrounds. To address these challenges, we propose a novel small object detection model for remote sensing images, named **SRS-DETR**, which is a variant of the DEtection TRansformer (DETR). Our main contributions are summarized as follows:

1. **Deformable Feature Correlation (DFC) module** — mitigates the high computational cost of the Multi-head Self-Attention mechanism in the transformer encoder and enhances the model's capability in detecting small objects.
2. **Dynamic Multi-scale Sequence Fusion (DMSF) module** — employs a dynamic design to efficiently fuse features at different scales, further improving the ability to detect small objects.
3. **GWIoU loss function** — a novel IoU loss specifically tailored for small object detection in remote sensing images, which combines GIoU with the Wasserstein distance to optimize the training process.

Experimental results demonstrate that the mAP of SRS-DETR reaches **85.3%**, **97.5%**, and **96.3%** on the LEVIR-Ship, SSDD, and RSOD datasets, respectively, outperforming current popular detection methods for remote sensing images.

**Keywords:** remote sensing images, small object detection, DETR, attention mechanism, IoU loss.

## News

- **[2026.08]** Source code released.

## Requirements

The experiments were conducted under the following environment:

- **OS:** Ubuntu 22.04
- **GPU:** NVIDIA GeForce RTX 4090
- **Python:** 3.10
- **PyTorch:** 2.1.0
- **CUDA:** 12.1

### Installation

```bash
# Clone the repository
git clone https://github.com/<your-username>/SRS-DETR.git
cd SRS-DETR

# Create a conda environment (optional)
conda create -n srs-detr python=3.10 -y
conda activate srs-detr

# Install dependencies
pip install -r requirements.txt
```

## Datasets

We evaluate SRS-DETR on three publicly available remote sensing datasets: **LEVIR-Ship**, **SSDD**, and **RSOD**.

| Dataset | Category | Images | Classes | Official Link |
|---|---|---|---|---|
| LEVIR-Ship | Optical (micro ship) | 3,896 | 1 (ship) | [WindVChen/LEVIR-Ship](https://github.com/WindVChen/LEVIR-Ship) |
| SSDD | SAR (ship) | 1,160 | 1 (ship) | [TianwenZhang0825/Official-SSDD](https://github.com/TianwenZhang0825/Official-SSDD) |
| RSOD | Optical (multi-class) | 976 | 4 (aircraft / oiltank / playground / overpass) | [RSIA-LIESMARS-WHU/RSOD-Dataset-](https://github.com/RSIA-LIESMARS-WHU/RSOD-Dataset-) |

### LEVIR-Ship

LEVIR-Ship is the first public micro-ship detection dataset for medium-resolution remote sensing images, captured by the multispectral cameras of the Gaofen-1 and Gaofen-6 satellites. It contains 2,320 images for training, 788 for validation, and 788 for testing.

- **Download:** https://github.com/WindVChen/LEVIR-Ship (annotations in YOLO format; COCO-format conversion tools are also provided)

### SSDD

SSDD (SAR Ship Detection Dataset) is the first publicly available SAR dataset for ship detection research. It contains 1,160 images of approximately 500×500 pixels and 2,456 ship instances. Following standard conventions, test images are named with suffixes `"1"` and `"9"`. In this work, the dataset is divided into training, validation, and test sets with a ratio of 8:1:1.

- **Download:** https://github.com/TianwenZhang0825/Official-SSDD
- **Google Drive:** https://drive.google.com/file/d/1glNJUGotrbEyk43twwB9556AdngJsynZ/view?usp=sharing
- **Baidu Pan:** https://pan.baidu.com/s/1Lpg28ZvMSgNXq00abHMZ5Q (password: `2021`)

### RSOD

RSOD is an open dataset for object detection in remote sensing images. It includes four classes: aircraft (4,993 instances / 446 images), playground (191 / 189), overpass (180 / 176), and oil tank (1,586 / 165). Similarly, the dataset is divided into training, validation, and test sets with a ratio of 8:1:1.

- **Download:** https://github.com/RSIA-LIESMARS-WHU/RSOD-Dataset-

### Dataset Preparation

Organize the downloaded datasets under a `datasets/` directory, for example:

```
datasets/
├── levir-ship/
│   ├── images/
│   └── labels/
├── ssdd/
│   ├── images/
│   └── labels/
└── rsod/
    ├── images/
    └── labels/
```

> Please adjust the directory structure to match the data loading code in this repository.

## Usage

### Training

```bash
# Train on LEVIR-Ship
python train.py --dataset levir-ship

# Train on SSDD
python train.py --dataset ssdd

# Train on RSOD
python train.py --dataset rsod
```

The training hyper-parameters for each dataset are listed below:

| Setting | LEVIR-Ship | SSDD | RSOD |
|---|---|---|---|
| Epochs | 200 | 120 | 150 |
| Image size | 512×512 | 608×608 | 640×640 |

Common settings: batch size = 4, workers = 8, learning rate = 1e-4, optimizer = AdamW.

### Testing / Evaluation

```bash
# Evaluate on the test set
python test.py --dataset levir-ship --weights <path-to-checkpoint>
```

## Results

### Main Results

mAP is reported at IoU = 0.5 (mAP@0.5).

**LEVIR-Ship**

| Method | mAP(%) | Params(M) | GFLOPs |
|---|---|---|---|
| Improved YOLOv5 | 76.9 | - | - |
| Ge et al. | 83.8 | - | - |
| Zhou et al. | 79.6 | 271.0 | 70.8 |
| ORFENet | 83.3 | 32.8 | 373.3 |
| Ship-DETR | 75.7 | 14.7 | 43.5 |
| DSFPAP-Net | 82.6 | 9.6 | 127.2 |
| DF-Net | 84.7 | - | - |
| YOLOv8 | 77.9 | 25.8 | 78.7 |
| YOLOv9 | 76.4 | 20.0 | 76.5 |
| YOLOv10 | 79.3 | 20.4 | 97.9 |
| YOLOv11 | 79.4 | 20.0 | 67.7 |
| YOLOv12 | 78.2 | 19.6 | 59.5 |
| RT-DETR | 81.0 | 20.0 | 61.0 |
| **SRS-DETR (ours)** | **85.3** | 20.2 | 61.7 |

**SSDD**

| Method | mAP(%) | Params(M) | GFLOPs |
|---|---|---|---|
| RSPrompter | 95.6 | - | - |
| Two-Way Assistant | 95.4 | - | - |
| FS-YOLO | 96.9 | 39.0 | - |
| YOLOv8 | 96.8 | 25.8 | 78.7 |
| YOLOv9 | 95.2 | 20.0 | 76.5 |
| YOLOv10 | 95.5 | 20.4 | 97.9 |
| YOLOv11 | 96.6 | 20.0 | 67.7 |
| YOLOv12 | 96.1 | 19.6 | 59.5 |
| RT-DETR | 96.2 | 20.0 | 61.0 |
| **SRS-DETR (ours)** | **97.5** | 20.2 | 61.7 |

**RSOD**

| Method | mAP(%) | Params(M) | GFLOPs |
|---|---|---|---|
| MRFF-YOLO | 88.3 | - | - |
| Dong et al. | 92.5 | - | - |
| Xu and Wu | 88.7 | - | - |
| CF2PN | 93.6 | 91.6 | - |
| ABNet | 94.2 | 42.8 | 141.3 |
| OYOLO | 92.8 | 40.4 | 36.0 |
| YOLOv8 | 93.1 | 25.8 | 78.7 |
| YOLOv9 | 92.1 | 20.0 | 76.5 |
| YOLOv10 | 90.2 | 20.4 | 97.9 |
| YOLOv11 | 95.1 | 20.0 | 67.7 |
| YOLOv12 | 84.9 | 19.6 | 59.5 |
| RT-DETR | 92.4 | 20.0 | 61.0 |
| **SRS-DETR (ours)** | **96.3** | 20.2 | 61.7 |

### Ablation Study (on LEVIR-Ship)

| baseline | DFC | DMSF | GWIoU | mAP(%) | Params(M) | GFLOPs |
|:---:|:---:|:---:|:---:|---|---|---|
| √ | | | | 81.0 | 20.0 | 61.0 |
| √ | √ | | | 82.4 | 19.9 | 57.2 |
| √ | √ | √ | | 84.6 | 20.2 | 61.7 |
| √ | √ | √ | √ | **85.3** | 20.2 | 61.7 |

## Citation

If you find this work useful in your research, please consider citing:

```bibtex
@article{yourname2026srsdetr,
  title   = {SRS-DETR: DETR for Small Object Detection in Remote Sensing Images},
  author  = {Your Name and Co-authors},
  journal = {Your Journal},
  year    = {2026}
}
```

## Acknowledgements

We thank the authors of the [LEVIR-Ship](https://github.com/WindVChen/LEVIR-Ship), [SSDD](https://github.com/TianwenZhang0825/Official-SSDD), and [RSOD](https://github.com/RSIA-LIESMARS-WHU/RSOD-Dataset-) datasets for making their data publicly available. This work also builds upon the open-source DETR-family implementations.
