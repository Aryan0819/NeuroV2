---
title: NeuroScan AI
emoji: 🧠
colorFrom: blue
colorTo: indigo
sdk: docker
app_port: 7860
---

<div align="center">

# 🧠 NeuroVision

### Brain MRI classification and segmentation research, in one web app.

[![Python 3.11](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-3.1-000000?logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.20-FF6F00?logo=tensorflow&logoColor=white)](https://www.tensorflow.org/)

**NeuroVision** (also known as **NeuroScan AI**) is a Flask application for exploring deep-learning workflows on brain MRI scans. Upload an image to view model-generated classification and segmentation results through a responsive web interface.

[Get started](#-get-started) · [Features](#-features) · [API](#-api)

</div>

> **Repository note:** This repository currently contains the project overview only. The application source, model architecture files, setup guide, and trained weights are not published here yet. The setup instructions below apply to a checkout of the NeuroVision source tree.

> [!IMPORTANT]
> **Research and education only.** This project is not a medical device and is not intended to diagnose, treat, cure, or prevent any condition. Its output must not guide patient care. MRI images and model results should be reviewed by qualified healthcare professionals.

## ✨ Features

- **MRI image analysis:** upload PNG, JPG, JPEG, or TIFF images for model inference.
- **Classification and segmentation:** run ResNet-50 and ResUNet models and display the predicted result and mask.
- **Ensemble inference:** combine available models and use test-time augmentation (TTA).
- **Scan history and accounts:** optional MongoDB persistence with a local JSON fallback.
- **Cloud image storage:** use Cloudinary for saved scan images.
- **Experiment utilities:** inspect dataset quality and compare model performance with the included scripts and notebook.

## 🧰 Technology

| Area | Tools |
| --- | --- |
| Web application | Python 3.11, Flask, Jinja templates |
| Deep learning | TensorFlow / Keras |
| Image processing | OpenCV, Pillow, scikit-image |
| Persistence | MongoDB (optional), local JSON fallback |
| Image hosting | Cloudinary |
| Deployment | Docker, Gunicorn |

## 🚀 Get started

### 1. Prepare Python

Python 3.11 is used by the included Docker image.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements-web.txt
```

For macOS or Linux, activate the environment with:

```bash
source .venv/bin/activate
python -m pip install -r requirements-web.txt
```

### 2. Configure environment variables

Copy the example file and set the required values:

```powershell
Copy-Item .env.example .env
```

Set `FLASK_SECRET_KEY` and the Cloudinary credentials in `.env`. The application validates these values at startup. `MONGO_URI` is optional; when it is unset, scan history uses local JSON storage.

```dotenv
FLASK_SECRET_KEY=replace-with-a-long-random-value
CLOUDINARY_CLOUD_NAME=your-cloud-name
CLOUDINARY_API_KEY=your-api-key
CLOUDINARY_API_SECRET=your-api-secret

# Optional: omit to use local JSON storage
MONGO_URI=
MONGO_DB_NAME=neuroscan_db
```

Never commit `.env` or real credentials.

### 3. Add model weights

The model architecture JSON files are included, but the trained weight files are large and excluded from Git. Obtain the matching weights through your authorized project or training workflow and place them in the project root:

| File | Model |
| --- | --- |
| `weights.hdf5` | ResNet-50 classification |
| `weights_seg.hdf5` | ResUNet segmentation |

The app requires both primary weight files to load its models. The optional secondary model files can be used when their corresponding artifacts are available.

### 4. Start the app

```powershell
python app.py
```

Open [http://127.0.0.1:5000](http://127.0.0.1:5000). The default bind address is localhost; configure `FLASK_HOST` and `FLASK_PORT` in `.env` if needed.

## 🐳 Docker

The included Dockerfile serves the app on port `7860`. Model weights must be present in the project root when building the image, and the required environment variables must be supplied at runtime.

```bash
docker build -t neurovision .
docker run --env-file .env -p 7860:7860 neurovision
```

For Hugging Face Spaces, configure secrets in the Space settings and provide model weights using an appropriate large-file storage method. Do not include secrets or private medical data in the image.

## 🔌 API

The Flask app serves the web interface at `/` and provides JSON endpoints, including:

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/api/health` | Check app and model status |
| `POST` | `/api/predict` | Submit an MRI image using the `file` form field |
| `GET` | `/api/stats` | Read application statistics |
| `GET` / `POST` | `/api/config` | Read or update app configuration |
| `GET` | `/api/history` | Retrieve scan history |

Prediction accepts PNG, JPG, JPEG, TIF, and TIFF images up to 16 MB. In the source tree, `app.py` defines the full endpoint behavior and response shapes.

## 🧪 Data and experiments

The source tree includes dataset-quality and model-comparison utilities plus an analysis notebook. The TCGA-LGG dataset and trained model checkpoints are not published in this repository; obtain and handle any data according to its license and privacy requirements. Consult `SETUP.md` in the source tree for dataset organization, experiment instructions, and troubleshooting.

## 🔐 Privacy and responsible use

- Treat MRI scans and generated reports as sensitive health information.
- Do not upload identifiable or patient data to a public deployment.
- Configure access controls and storage retention appropriately before any deployment.
- Model predictions can be wrong or incomplete; they are not clinical advice.

## 🤝 Contributing

Contributions that improve the application, documentation, or reproducibility are welcome.

