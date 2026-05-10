# Cats vs Dogs — Transfer Learning Classifier

Binary image classification (cat vs dog) using **MobileNetV2 transfer learning** with 98.98% validation accuracy.

## Results

| Metric | Original (3-layer CNN) | Improved (MobileNetV2) |
|--------|----------------------|------------------------|
| Validation Accuracy | 89.00% | **98.98%** |
| Parameters | ~19,030,000 | **2,259,265** |
| Cat F1-Score | 0.89 | 0.9898 |
| Dog F1-Score | 0.89 | 0.9899 |
| Misclassified | ~550 / 5,000 | 203 / 19,999 |

### Key Improvements
- **Transfer Learning**: MobileNetV2 pretrained on ImageNet replaces from-scratch 3-layer CNN
- **GlobalAveragePooling2D**: Replaces Flatten, reducing parameters by 88%
- **Dropout(0.5)** + Color Augmentation (brightness/contrast)
- **Two-phase Training**: Freeze backbone → train head → fine-tune top layers
- **ReduceLROnPlateau** + EarlyStopping for adaptive training

## Project Structure

```
├── train.py                              # End-to-end training pipeline
├── app.py                                # Flask API server
├── index.html                            # Static web demo (double-click to run)
├── head_weights.json                     # Classifier head weights (for browser inference)
├── 启动.vbs                               # One-click launcher for Flask version
├── output/
│   ├── model.keras                       # Trained Keras model (~24.5 MB)
│   ├── training_curves.png               # Accuracy & loss curves
│   ├── confusion_matrix.png              # Validation confusion matrix
│   └── classification_report.txt         # Precision / recall / F1 report
├── Experiment_Report_Comparison_EN.docx   # English comparison report
└── 实验改进报告_对比.docx                  # Chinese comparison report
```

## Quick Start

### Option 1: Static HTML (Recommended)

```
Double-click index.html → wait for model to load (~10 MB, cached after first visit) → drag & drop an image
```
First launch requires internet to download MobileNetV2 backbone from TF Hub. Subsequent visits load from browser cache instantly.

### Option 2: Flask Server (Offline)

```
Double-click 启动.vbs → opens http://127.0.0.1:5001
```

Or manually:
```bash
pip install tensorflow flask pillow
python app.py
```

### Retrain the Model

```bash
pip install tensorflow matplotlib scikit-learn pillow
python train.py
```
The script automatically downloads the dataset (~800 MB), preprocesses images, trains with two-phase transfer learning, and saves all outputs to `output/`.

## Dependencies

| Component | Packages |
|-----------|----------|
| Training | `tensorflow>=2.17`, `numpy`, `matplotlib`, `scikit-learn`, `pillow` |
| Flask server | `tensorflow`, `flask`, `pillow` |
| Static HTML | None — runs entirely in browser via TF.js CDN |

## Model Architecture

```
Input (160x160x3)
  → MobileNetV2 backbone (pretrained, 2,257,984 params)
  → GlobalAveragePooling2D
  → Dropout(0.5)
  → Dense(1, sigmoid) (1,281 trainable params)
  → Output: P(dog)
```
