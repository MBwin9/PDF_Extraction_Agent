# PDF Extraction Agent

An agentic pipeline for extracting **tables** from insurance PDFs (or other complex documents) into structured formats (CSV/Excel).  
Combines **PDF-Extract-Kit**, **PaddleOCR (PP-StructureV2)**, and utility scripts for preprocessing, page rendering, and table parsing.

---

## 🚀 Features
- Convert PDFs into page-level images (`fitz` / PyMuPDF).
- Extract tables using **PaddleOCR Table (SLANet)**.
- Output results as **Excel, CSV, or HTML**.
- Works on **CPU or GPU** (auto-detect GPU; fallback to CPU).
- Process a single page or the full PDF.

---

## 🛠 Requirements
- **Python 3.10+**
- **Conda**
- Optional: **NVIDIA GPU** with CUDA/cuDNN

---

## 🔐 Logging into the Instance (SSH)

### macOS/Linux
ssh-keygen -t ed25519 -C "your_email@example.com"
ssh -i ~/.ssh/<your_key> ubuntu@<YOUR_INSTANCE_IP>

### Windows
Use **PowerShell** (same as above) or **PuTTY** with your private key.

### After login
conda activate pek
cd ~/work/PDF-Extraction-Agent

### Upload / Download files
# Upload a local PDF to the instance
scp -i ~/.ssh/<your_key> /path/to/file.pdf ubuntu@<YOUR_INSTANCE_IP>:~/work/PDF-Extraction-Agent/data/

# Download results back to your machine
scp -i ~/.ssh/<your_key> ubuntu@<YOUR_INSTANCE_IP>:~/work/PDF-Extraction-Agent/output/page6/*.xlsx .

---

## 📦 Installation

git clone https://github.com/MBwin9/PDF_Extraction_Agent.git
cd PDF_Extraction_Agent

conda create -n pek python=3.10 -y
conda activate pek
pip install -r requirements.txt

---

## ▶️ Quick Start (single page demo)

# 1) Render page 6 from your PDF into an image
python - <<'PY'
import fitz, os
pdf="data/file.pdf"  # <-- put your PDF at data/file.pdf or change this path
out="data/page_images"; os.makedirs(out, exist_ok=True)
doc=fitz.open(pdf)
pix=doc[5].get_pixmap(dpi=300)  # page 6 (index 5)
pix.save(os.path.join(out,"page_006.png"))
print("Saved:", os.path.join(out,"page_006.png"))
PY

# 2) Extract a table from that page into Excel/HTML (CPU mode)
paddleocr \
  --image_dir data/page_images/page_006.png \
  --type structure \
  --lang en \
  --output output/page6 \
  --ocr true \
  --use_gpu false

---

## 📂 Project Structure
PDF_Extraction_Agent/
├─ configs/               # YAML configs for tasks
├─ data/                  # Input PDFs/images (ignored by git)
├─ output/                # OCR results (ignored by git)
├─ vendor/PDF-Extract-Kit # External extraction toolkit
├─ scripts/               # Runners and utilities
├─ requirements.txt       # Dependencies
└─ README.md              # This file

---

## 🧰 Troubleshooting
- **cuDNN / GPU errors**: add `--use_gpu false` to run on CPU.
- **No tables detected**: re-render at higher DPI (e.g., 400) or crop the table region and rerun.
- **Permissions when pushing**: ensure your Git remote is HTTPS and you’re authenticated.
