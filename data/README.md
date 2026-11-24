# BDD100K Dataset – Architecture & Cloud Setup

## Why Is This Directory Empty in Git?

The **BDD100K** dataset is intentionally excluded from this repository because of its large storage footprint.

- Validation split: approximately 1.3 GB.
- Full training and validation splits: more than 7 GB.
- Dataset files: over 100,000 images and segmentation masks.

Instead, this project follows a **Cloud-Native Workflow**:

- **Local Machine (GitHub Repository):** Contains source code, configuration files, model weights, and documentation.
- **Google Colab / Cloud GPU:** Downloads and stores the dataset directly in the cloud runtime for training and evaluation.

This approach keeps the GitHub repository lightweight while allowing access to the complete dataset when needed.

---

## Expected Directory Structure

When running training or evaluation on Google Colab or a local GPU workstation, the dataloader (`lib/dataset/bdd.py`) expects the following directory structure:

```text
data/
├── README.md
└── bdd100k/
    ├── images/
    │   ├── train/
    │   └── val/
    │
    ├── det_annotations/
    │   ├── bdd100k_labels_images_train.json
    │   └── bdd100k_labels_images_val.json
    │
    ├── da_seg_annotations/
    │   ├── train/
    │   └── val/
    │
    └── ll_seg_annotations/
        ├── train/
        └── val/
```

## Multi-Task Dataset Specifications

YOLOP uses the BDD100K dataset to train and evaluate three autonomous driving perception tasks.

| Directory | Perception Task | Format | Description |
|---|---|---|---|
| `images/` | Input Visual Frames | `.jpg` | RGB road scene images (1280 × 720 pixels), resized to 640 × 640 during preprocessing. |
| `det_annotations/` | Object Detection | `.json` | 2D bounding boxes for traffic objects such as cars, buses, trucks, and pedestrians. |
| `da_seg_annotations/` | Drivable Area Segmentation | `.png` | Pixel-level masks identifying drivable road areas. |
| `ll_seg_annotations/` | Lane Line Segmentation | `.png` | Pixel-level masks identifying lane markings. |

### Dataset Splits

- Training: 70,000 images.
- Validation: 10,000 images.

## Dataset Configuration

Dataset paths are configured in `lib/config/default.py`:

```python
_C.DATASET.DATAROOT = 'data/bdd100k/images'
_C.DATASET.LABELROOT = 'data/bdd100k/det_annotations'
_C.DATASET.MASKROOT = 'data/bdd100k/da_seg_annotations'
_C.DATASET.LANEROOT = 'data/bdd100k/ll_seg_annotations'
```

These paths must match the directory structure described above.

---

## Google Colab Setup

Follow these steps to prepare the dataset in Google Colab.

### 1. Clone the Repository

```bash
!git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
%cd YOUR_REPOSITORY
```

Replace the repository URL with your actual GitHub repository.

### 2. Install Dependencies

```bash
!pip install kagglehub gdown
```

### 3. Download Images and Detection Annotations

The following script downloads the BDD100K validation images and detection annotations using KaggleHub.

```python
import os
import kagglehub

# Download BDD100K
dataset_path = kagglehub.dataset_download("marquis03/bdd100k")

# Create dataset directories
os.makedirs("data/bdd100k/images", exist_ok=True)

# Link validation images
os.symlink(
    f"{dataset_path}/val/images",
    "data/bdd100k/images/val"
)

# Link detection annotations
os.symlink(
    f"{dataset_path}/val/annotations",
    "data/bdd100k/det_annotations"
)

print("BDD100K images and detection annotations ready!")
```

### 4. Download Segmentation Masks

The full multi-task evaluation requires additional ground-truth segmentation masks.

Download the Drivable Area and Lane Line annotations using the following Google Drive resources:

```python
import os
import zipfile
import gdown

os.makedirs("data/bdd100k", exist_ok=True)

# Drivable Area Segmentation
gdown.download(
    id="1xy_DhUZRHR8pdmH6Blp1si16_6-02116",
    output="da_seg.zip",
    quiet=False
)

with zipfile.ZipFile("da_seg.zip", "r") as archive:
    archive.extractall("data/bdd100k/")

os.remove("da_seg.zip")

# Lane Line Segmentation
gdown.download(
    id="1lD9l_R6-90_b63_836-2561046",
    output="ll_seg.zip",
    quiet=False
)

with zipfile.ZipFile("ll_seg.zip", "r") as archive:
    archive.extractall("data/bdd100k/")

os.remove("ll_seg.zip")

print("Segmentation annotations downloaded!")
```

**Important:** After extraction, verify that the folders are named:

- `da_seg_annotations/`
- `ll_seg_annotations/`

Both folders must be located inside `data/bdd100k/` and contain the expected `train/` and `val/` directories.

### 5. Verify Dataset Structure

Run the following script to check the downloaded files:

```python
import os

required_paths = [
    "data/bdd100k/images/val",
    "data/bdd100k/det_annotations",
    "data/bdd100k/da_seg_annotations",
    "data/bdd100k/ll_seg_annotations"
]

for path in required_paths:
    if os.path.exists(path):
        print(f"[OK] {path}")
    else:
        print(f"[MISSING] {path}")
```

---

## Running Training and Evaluation

Once the dataset is ready, you can run the project scripts.

**Training:**

```bash
python tools/train.py
```

**Evaluation:**

```bash
python tools/test.py
```

Make sure the model weights and all required dataset annotations are available before execution.
