

# 🎨 Cartoonize

A web app that turns your photos and short videos into cartoon-style images using **White-Box Cartoonization**, a deep learning model built with TensorFlow.

> ℹ️ The block at the very top of this file is required by Hugging Face Spaces for deployment configuration. GitHub just renders it as plain text — it doesn't affect anything here.

🔗 **Live Demo:** [https://huggingface.co/spaces/Amitesh-3114/Cartoonize](https://huggingface.co/spaces/Amitesh-3114/Cartoonize)

---

## ✨ Features

- 🖼️ Cartoonize images directly in the browser
- 🎬 Cartoonize short video clips (with audio preserved)
- ⚙️ Configurable video trimming, resolution, and frame rate via `config.yaml`
- 🐳 Fully containerized with Docker — runs anywhere Docker runs

---

## 🛠️ Tech Stack

- **Flask** – web server / app framework
- **TensorFlow 2.1 + tf_slim** – White-Box Cartoonization model inference
- **OpenCV / Pillow** – image processing
- **ffmpeg + scikit-video** – video processing
- **Gunicorn** – production WSGI server
- **Docker** – containerized deployment

---

## 📁 Project Structure

```
Cartoonize/
├── app.py                      # Flask app & main routes
├── config.yaml                 # App configuration (local mode, video settings, etc.)
├── gcloud_utils.py              # Optional Google Cloud Storage helpers (used only if run_local: false)
├── video_api.py                 # Optional Algorithmia GPU inference helper (used only if run_local: false)
├── requirements.txt             # Python dependencies (pinned for TF 2.1 compatibility)
├── Dockerfile                   # Container build for deployment
├── white_box_cartoonizer/       # Cartoonization model + weights
│   ├── cartoonize.py
│   ├── network.py
│   ├── guided_filter.py
│   └── saved_models/
├── static/
│   ├── sample_images/           # Example before/after images
│   ├── cartoonized_images/       # Generated image output (runtime)
│   └── uploaded_videos/          # Generated video output (runtime)
└── templates/
    ├── index_cartoonized.html
    └── style.css
```

---

## 🚀 Running Locally

### Prerequisites
- Python 3.7
- `ffmpeg` installed and available on your PATH

### 1. Clone the repository
```bash
git clone https://github.com/Amitesh-3114/Cartoonize.git
cd Cartoonize
```

### 2. Create a virtual environment & install dependencies
```bash
python3 -m venv cartoonize
source cartoonize/bin/activate   # on Windows: cartoonize\Scripts\activate
pip install -r requirements.txt
```

### 3. Configure
Check `config.yaml` — for local runs, keep `run_local: true` and `gpu: false` (unless you have a configured GPU).

### 4. Run the app
```bash
python app.py
```
The app will be available at `http://127.0.0.1:8080`.

---

## ☁️ Deployment

This app is deployed as a **Docker Space** on [Hugging Face Spaces](https://huggingface.co/spaces) (free CPU tier). The repo is set up to deploy as-is:

- The YAML block at the top of this `README.md` provides the required Spaces configuration (`sdk: docker`, `app_port: 7860`)
- `Dockerfile` listens on port `7860` as expected by Spaces
- `config.yaml` has `run_local: true` and `gpu: false` so no cloud credentials are needed

To deploy your own copy:
```bash
git remote add space https://huggingface.co/spaces/<your-username>/<space-name>
git push space main
```

> ⚠️ Free Spaces sleep after a period of inactivity and "wake up" on the next visit (cold start ~30–60s). Storage isn't persistent across restarts, which is expected for this kind of demo.

---

## ⚙️ Configuration Reference (`config.yaml`)

| Key | Description |
|---|---|
| `run_local` | `true` = process everything in-app (no cloud dependencies). Set to `false` only if you've configured Google Cloud Storage + Algorithmia. |
| `gpu` | Whether to use a GPU for inference. Set to `false` for CPU-only environments. |
| `trim-video` | If `true`, trims uploaded videos to `trim-video-length` seconds before processing. |
| `trim-video-length` | Max seconds to process from an uploaded video. |
| `output_frame_rate` | Frame rate for output videos (used if `original_frame_rate: false`). |
| `original_frame_rate` | If `true`, keeps the input video's original frame rate. |
| `original_resolution` | If `true`, keeps the input video's original resolution. |
| `resize-dim` | Output video width (maintains aspect ratio) when `original_resolution: false`. |

---

## 📝 License

This project is licensed under **CC BY-NC-SA 4.0** (Attribution-NonCommercial-ShareAlike) — see [LICENSE](LICENSE) for details, due to its use of code from the original research. Free to use and share for non-commercial purposes with attribution, but not for commercial use.
