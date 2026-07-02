**Ultralytics YOLO** (YOLO11 / YOLO26 / YOLOv8) supports multiple computer vision tasks with a clean, unified API.

### Supported Tasks

| Task                        | Model Suffix | Purpose                                      | Pretrained Example          |
|-----------------------------|--------------|----------------------------------------------|-----------------------------|
| **Detect** (Object Detection) | (default)   | Bounding boxes + class + confidence         | `yolo11n.pt`               |
| **Segment** (Instance Segmentation) | `-seg`     | Bounding boxes + pixel masks                | `yolo11n-seg.pt`           |
| **Pose** (Keypoint Detection) | `-pose`     | Bounding boxes + keypoints (skeleton)       | `yolo11n-pose.pt`          |
| **Classify** (Image Classification) | `-cls`     | Whole-image class probabilities             | `yolo11n-cls.pt`           |
| **OBB** (Oriented Bounding Boxes) | `-obb`     | Rotated bounding boxes                      | `yolo11n-obb.pt`           |

**Tracking** (ByteTrack / BoTSORT) works with Detect, Segment, and Pose models.

---

### 1. Installation

```bash
pip install -U ultralytics
```

Verify:
```python
from ultralytics import YOLO
model = YOLO("yolo11n.pt")
```

---

### 2. Loading / Reloading a Model

```python
from ultralytics import YOLO

# Pretrained
model = YOLO("yolo11n.pt")           # Detection
model = YOLO("yolo11n-seg.pt")       # Segmentation
model = YOLO("yolo11n-pose.pt")      # Pose
model = YOLO("yolo11n-cls.pt")       # Classification
model = YOLO("yolo11n-obb.pt")       # OBB

# Custom model
model = YOLO("path/to/best.pt")
model = YOLO("path/to/last.pt")      # Resume training
```

---

### 3. Prediction / Inference

```python
model = YOLO("yolo11n.pt")   # Change according to task

results = model("image.jpg")
results = model(["img1.jpg", "img2.jpg"])
results = model("video.mp4")
results = model(0)                    # Webcam
results = model("folder_path/")

# Parameters
results = model("image.jpg", conf=0.25, iou=0.7, imgsz=640, save=True)
```

**Streaming**:
```python
for result in model("video.mp4", stream=True):
    result.show()
    result.save()
```

**Access results**:
```python
for r in results:
    boxes = r.boxes          # Detection/Seg/Pose/OBB
    masks = r.masks          # Segmentation
    keypoints = r.keypoints  # Pose
    probs = r.probs          # Classification
    r.plot()                 # Visualise
```

---

### 4. Evaluation / Validation + Visualization

```python
model = YOLO("path/to/best.pt")

metrics = model.val(data="your_data.yaml", imgsz=640, batch=16, plots=True)

# Key metrics
print("mAP50-95:", metrics.box.map)
print("mAP50:", metrics.box.map50)
print("Precision:", metrics.box.p)
print("Recall:", metrics.box.r)
```

**Visualizations generated automatically** (when `plots=True`):

- `confusion_matrix.png` & `confusion_matrix_normalized.png`
- `PR_curve.png`, `F1_curve.png`, `P_curve.png`, `R_curve.png`
- `results.png` (during training)

**View plots**:
```python
import matplotlib.pyplot as plt
import matplotlib.image as mpimg

img = mpimg.imread("runs/detect/val/confusion_matrix.png")
plt.figure(figsize=(10, 8))
plt.imshow(img)
plt.axis('off')
plt.show()
```

---

### 5. Training + Visualizing Training / Validation Accuracy & Loss

**Python**:
```python
model = YOLO("yolo11n.pt")   # or task-specific model

results = model.train(
    data="data.yaml",
    epochs=100,
    imgsz=640,
    batch=16,
    name="my_experiment",
    plots=True,          # Generates results.png, curves, etc.
    patience=50
)
```

**After training**, Ultralytics saves these key visualizations in `runs/detect/train/` (or `segment/`, `classify/`, etc.):

- **`results.png`** → Best overall view: Training & Validation loss curves + Precision, Recall, mAP curves.
- Loss curves (box loss, cls loss, dfl loss, etc.)
- mAP curves (validation accuracy metric)

**Display training results**:
```python
import matplotlib.pyplot as plt
import matplotlib.image as mpimg

# Show main results plot
plt.figure(figsize=(12, 10))
img = mpimg.imread("runs/detect/train/results.png")
plt.imshow(img)
plt.axis('off')
plt.title("Training & Validation Metrics")
plt.show()
```

**TensorBoard** (recommended for interactive / live monitoring):

1. Enable TensorBoard:
   ```bash
   yolo settings tensorboard=True
   ```

2. Start training (it will log automatically).

3. Launch TensorBoard:
   ```bash
   tensorboard --logdir runs/detect/train   # or your project/name
   ```

4. Open `http://localhost:6006` in browser to see live graphs of:
   - Losses (train/val)
   - mAP, Precision, Recall
   - Learning rate
   - Histograms, etc.

**CLI Training**:
```bash
yolo detect train model=yolo11n.pt data=data.yaml epochs=100 imgsz=640 plots=True
# Use segment train, classify train, etc. for other tasks
```

---

### 6. Retrain / Continue Training

**Resume**:
```python
model = YOLO("runs/detect/train/weights/last.pt")
model.train(resume=True)
```

**Continue with more data**:
```python
model = YOLO("path/to/best.pt")
model.train(data="updated_data.yaml", epochs=50, lr0=0.001)  # Fine-tune
```

---

### 7. Other Important Operations

**Export**:
```python
model.export(format="onnx")        # onnx, engine (TensorRT), tflite, openvino, etc.
```

**Tracking**:
```python
results = model.track(source="video.mp4", show=True)
```

**Benchmark**:
```python
model.benchmark()
```

**Hyperparameter Tuning**:
```python
model.tune(data="data.yaml", epochs=10, iterations=20)
```

---

### 8. Dataset Format

Each task has a specific format (images + labels). See official docs for full examples.

**Official Documentation**: [https://docs.ultralytics.com/](https://docs.ultralytics.com/)

This guide now includes **all major tasks**, prediction, evaluation, training, resuming, and **visualization of training/validation accuracy, loss, mAP, confusion matrix**, etc. 

Start experimenting with small models (`n` or `s` variant) and `coco8.yaml` for quick tests. Let me know if you need a full example for a specific task!
