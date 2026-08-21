# SRS-DETR: DETR for Small Object Detection in Remote Sensing Images

This is the code of the paper **"SRS-DETR: DETR for Small Object Detection in Remote Sensing Images"**.

> SRS-DETR is a novel DETR-variant model specifically designed for small object detection in remote sensing images. It introduces three key designs — the **Deformable Feature Correlation (DFC)** module, the **Dynamic Multi-scale Sequence Fusion (DMSF)** module, and the **GWIoU** loss function — to jointly improve detection accuracy while keeping the computational cost under control.

[![Paper](https://img.shields.io/badge/Paper-here-green.svg)]()
[![License](https://img.shields.io/badge/License-MIT-blue.svg)]()

---

## Abstract

In the field of remote sensing image processing, small object detection presents a significant challenge due to the diminutive size of the objects, limitations in image resolution, and the difficulty of recognizing objects within complex backgrounds. To address these challenges, we propose a novel small object detection model for remote sensing images, named **SRS-DETR**, which is a variant of the DEtection TRansformer (DETR). Our main contributions are summarized as follows:

1. **Deformable Feature Correlation (DFC) module** — mitigates the high computational cost of the Multi-head Self-Attention mechanism in the transformer encoder and enhances the model's capability in detecting small objects.
2. **Dynamic Multi-scale Sequence Fusion (DMSF) module** — employs a dynamic design to efficiently fuse features at different scales, further improving the ability to detect small objects.
3. **GWIoU loss function** — a novel IoU loss specifically tailored for small object detection in remote sensing images, which combines GIoU with the Wasserstein distance to optimize the training process.

Experimental results demonstrate that the mAP of SRS-DETR reaches **85.3%**, **97.5%**, and **96.3%** on the LEVIR-Ship, SSDD, and RSOD datasets, respectively, outperforming current popular detection methods for remote sensing images.

**Keywords:** remote sensing images, small object detection, DETR, attention mechanism, IoU loss.

<img width="567" height="210" alt="image" src="https://github.com/user-attachments/assets/3b3b66d8-6a17-4b6b-8031-93ad7351ea85" />


## Requirements

The experiments were conducted under the following environment:

- **OS:** Ubuntu 22.04
- **GPU:** NVIDIA GeForce RTX 4090
- **Python:** 3.10
- **PyTorch:** 2.1.0
- **CUDA:** 12.1

## Datasets

We evaluate SRS-DETR on three publicly available remote sensing datasets: **LEVIR-Ship**, **SSDD**, and **RSOD**.

LEVIR-Ship | Optical (micro ship) | 3,896 | 1 (ship) | [WindVChen/LEVIR-Ship](https://github.com/WindVChen/LEVIR-Ship) |
SSDD | SAR (ship) | 1,160 | 1 (ship) | [TianwenZhang0825/Official-SSDD](https://github.com/TianwenZhang0825/Official-SSDD) |
RSOD | Optical (multi-class) | 976 | 4 (aircraft / oiltank / playground / overpass) | [RSIA-LIESMARS-WHU/RSOD-Dataset-](https://github.com/RSIA-LIESMARS-WHU/RSOD-Dataset-) |

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


## Deformable Feature Correlation Module

<img width="377" height="356" alt="image" src="https://github.com/user-attachments/assets/02f1b18e-a2c4-4383-88b6-210eefc3159e" />

We propose the Deformable Feature Correlation (DFC) module. Specifically, we improve the Multi-head Self-Attention mechanism by introducing Deformable Attention, which reduces computational cost. Additionally, we combine the deformable sampled features with different image feature information, allowing the model to efficiently handle features at various scales and better fuse context information. This enhancement significantly improves the ability of the model to detect small objects.

## Dynamic Multi-scale Sequence Fusion Module

<img width="555" height="415" alt="image" src="https://github.com/user-attachments/assets/d19eeb45-d187-4385-9c4f-2295aa54cc40" />

we propose a Dynamic Multi-scale Sequence Fusion (DMSF) module, We perform dynamic multi-scale sequence fusion operations on the {S3, S4, S5} feature maps. Specifically, each feature map in the DMSF module first undergoes a convolution operation. Next, a dynamic upsample operation is performed on the {S4, S5} feature maps. Dynamic upsample adjusts feature maps of different resolutions to a uniform resolution framework. This scaling is dynamic and dependent on the specific dimensions of the input feature maps, ensuring effective fusion of feature maps at different scales. 

## GWIoU loss function
Check the original text after the paper is published

### Training

| Setting | LEVIR-Ship | SSDD | RSOD |
|---|---|---|---|
| Epochs | 200 | 120 | 150 |
| Image size | 512×512 | 608×608 | 640×640 |

Common settings: batch size = 4, workers = 8, learning rate = 1e-4, optimizer = AdamW.


## Acknowledgements

We thank the authors of the [LEVIR-Ship](https://github.com/WindVChen/LEVIR-Ship), [SSDD](https://github.com/TianwenZhang0825/Official-SSDD), and [RSOD](https://github.com/RSIA-LIESMARS-WHU/RSOD-Dataset-) datasets for making their data publicly available. This work also builds upon the open-source DETR-family implementations.
