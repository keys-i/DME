# DME — Diabetic Macular Edema Detection from Fundus Images

A full-stack software engineering student project for automated Diabetic Macular Edema (DME) detection from retinal fundus images using a deep learning classifier, served through a Vite + React web interface.

The system is split into:
- Backend (Python, `app.py`): preprocessing and model inference
- Frontend (Vite + React): image upload and results display

Medical disclaimer: This repository is for learning and research. It is not a certified medical device and must not be used as the sole basis for clinical diagnosis or treatment.

---

## Features

- Upload fundus images (JPG/PNG)
- Deterministic preprocessing (resize and green-channel enhancement)
- DenseNet-style CNN classifier trained with weighted cross-entropy to mitigate class imbalance
- Outputs DME severity prediction (e.g., Healthy / Moderate / Severe) with optional confidence/probabilities
- Simple UI designed for fast screening-style workflows

---

## Background

DME is a common cause of vision impairment among people with diabetes. Traditional screening and grading can be:
- time-consuming and difficult to scale
- dependent on clinician expertise (inter-observer variability)
- challenging for subtle early-stage cases
- limited by class imbalance in typical datasets

This project focuses on building a pipeline that is fast, consistent, and reproducible, with a deployment-minded architecture (client UI + inference service).

---

## Approach (high level)

Preprocessing:
- Resize images to a stable model input resolution
- Extract the green channel to increase contrast of relevant retinal structures in fundus imagery

Model:
- DenseNet-style feature extractor with a classification head
- Trained using weighted cross-entropy to reduce majority-class bias

Recommended evaluation beyond accuracy:
- Macro F1
- Per-class recall (especially Moderate/Severe)
- Confusion matrix

---

## Tech stack

- Frontend: Vite + React
- Backend: Python (`app.py`)
- ML: CNN classifier (DenseNet-style), weighted loss

---

## Quickstart

### 1) Run the backend

Requirements:
- Python 3.x

Commands:
```bash
git clone https://github.com/keys-i/DME.git
cd DME

pip install -r requirements.txt
python app.py
````

This starts the inference service. The port and base URL depend on how `app.py` is configured.

---

### 2) Run the frontend (Vite + React)

Requirements:

* Node.js (18+ recommended)

Commands:

```bash
cd frontend/dme_front
npm install
npm run dev
```

Vite will print the local URL (commonly `http://localhost:5173`). Open it, upload an image, and view the prediction.

---

## Configuration notes

CORS:
If the browser blocks requests, enable CORS in `app.py` for the Vite dev origin (`http://localhost:5173`) or the deployed frontend origin.

API base URL:
If the frontend expects an environment variable, set it when running Vite, for example:

```bash
VITE_API_BASE_URL=http://localhost:5000 npm run dev
```

The exact variable name and default URL depend on the frontend implementation.

---

## API (expected contract)

This project is designed around a simple prediction endpoint.

Endpoint:

* `POST /predict`

Input:

* fundus image file (multipart/form-data) or base64 image payload (depending on implementation)

Output:

* predicted class label
* optional confidence and per-class probabilities

Example response:

```json
{
  "prediction": "Moderate DME",
  "confidence": 0.92,
  "probabilities": {
    "Healthy": 0.03,
    "Moderate DME": 0.92,
    "Severe DME": 0.05
  }
}
```

---

## Limitations

* Not clinically validated; results depend heavily on dataset quality, imaging conditions, and label consistency
* Fundus-only grading may not match OCT-based clinical assessment in some cases
* Confidence values may be uncalibrated unless calibration is explicitly implemented
* Cross-device and cross-population generalization is not guaranteed without external validation

---

## Roadmap

* Add image quality checks (blur, illumination, non-fundus detection)
* Add explainability (Grad-CAM overlays) to support model interpretation
* Calibrate confidence outputs (temperature scaling)
* Export to ONNX for faster and more portable inference
* Add basic audit metadata (model version, timestamp, input hash)
