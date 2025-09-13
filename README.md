# 🖼️ Object Insertion Dataset Builder (Inpainting + Filtering)

This repository provides a modular pipeline to **create datasets for object insertion tasks** 
using *inpainting* and *filtering*.  
Currently supports **[LaMa](https://github.com/saic-mdal/lama)** as the inpainting backend, 
but the design allows easy integration of other models (e.g., Stable Diffusion Inpainting, ZITS).

---

## 🚀 Features
- 🔄 **Modular Inpainting**: plug-and-play different models via a common interface.
- 🧹 **Filtering**: manual or automatic quality filtering (SSIM, CLIP, artifact detection, etc.).
- 📂 **Dataset Pipeline**: raw → inpainted → filtered.
- 🛠️ **Extensible**: easily add new inpainting models or filters.
- 🧪 **Tested**: with unit tests for each module.

---

## ⚙️ Installation

```bash
git clone https://github.com/yourname/object-insertion-dataset.git
cd object-insertion-dataset

# Create venv
python -m venv venv
source venv/bin/activate   # or .\venv\Scripts\activate on Windows

# Install dependencies
pip install -r requirements.txt
````

---

## ▶️ Usage

### 1. Run Inpainting

```bash
python scripts/run_inpainting.py \
    --input_dir data/raw/images \
    --mask_dir data/raw/masks \
    --output_dir data/inpainted/lama \
    --config_p configs/lama/config.yaml \
    --ckpt_p weights/lama.ckpt
```

### 2. Run Filtering

```bash
python scripts/run_filter.py \
    --input_dir data/inpainted/lama \
    --output_dir data/filtered/lama \
    --method auto_quality
```

### 3. Build Full Dataset

```bash
python scripts/build_dataset.py --config config.yaml
```

---

## 🔧 Adding a New Inpainting Model

1. Create a folder inside `models/`, e.g. `models/sd_inpainting/`.
2. Implement a class inheriting from `BaseInpainter`:

```python
from models.base_model import BaseInpainter

class SDInpainter(BaseInpainter):
    def __init__(self, model_id="stabilityai/stable-diffusion-2-inpainting"):
        # load huggingface model here
        pass

    def inpaint(self, image, mask):
        # run inference
        return result
```

3. Use it in pipeline:

```python
from models.sd_inpainting.model import SDInpainter
inpainter = SDInpainter()
```

---

## 🔧 Adding a New Filter

1. Create a new file in `filters/`.
2. Implement a class with method `filter(image, metadata=None) -> bool`.

```python
class BrightnessFilter:
    def filter(self, image, metadata=None) -> bool:
        # Example: reject images that are too dark
        return image.mean() > 30
```

---

## 📊 Example Dataset Flow

1. Raw images + masks → `data/raw/`
2. Run inpainting (LaMa) → results in `data/inpainted/lama/`
3. Run filtering → final dataset in `data/filtered/lama/`

---

## 🧪 Tests

```bash
pytest tests/
```

---

## 📌 TODO

* [ ] Add Stable Diffusion Inpainting
* [ ] Add CLIP-based semantic filtering
* [ ] Add Dockerfile for reproducibility

