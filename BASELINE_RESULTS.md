# YOLOP - Official Baseline Evaluation (BDD100K Validation Set)

- **Hardware / Environment:** Google Colab (Tesla T4 GPU), PyTorch 2.14.1, Python 3.13
- **Weights:** `weights/End-to-end.pth`
- **Dataset:** BDD100K `val` split (10,000 images, ~108,000 object instances)

| Task | Metric | Result | Official Paper |
| :--- | :--- | :---: | :---: |
| **Object Detection** | `mAP@0.5` | **76.5%** (`0.765`) | 76.5% |
| **Object Detection** | `Recall (R)` | **89.2%** (`0.892`) | 89.2% |
| **Object Detection** | `mAP@0.5:0.95` | **43.9%** (`0.439`) | — |
| **Drivable Area Seg.** | `mIoU` | **91.5%** (`0.915`) | 91.5% |
| **Drivable Area Seg.** | `Pixel Accuracy` | **97.4%** (`0.974`) | — |
| **Lane Line Seg.** | `Accuracy` | **70.6%** (`0.706`) | 70.5% |
| **Lane Line Seg.** | `IoU / mIoU` | **26.2%** / **62.3%** | 26.2% (IoU) |
| **Speed (T4 GPU)** | `Inference + NMS` | **12.1 ms/frame** (`10.9 ms + 1.2 ms`) | ~23 ms (V100) |
