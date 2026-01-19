# ✒️ Signature Detection & Verification from PDF Documents

A lightweight pipeline that detects signatures inside PDF documents using a YOLO-based detector, then verifies whether two signature crops match using a Siamese Network. Upload a PDF, locate signatures, and verify them against known/reference signatures with similarity scores.

---

## Table of Contents
- [Overview](#overview)
- [Highlights](#highlights)
- [How it works](#how-it-works)
- [Quick Start](#quick-start)
- [Usage Example](#usage-example)
- [Model Details](#model-details)
- [Tips for best results](#tips-for-best-results)
- [Project structure (overview)](#project-structure-overview)
- [Contributing](#contributing)
- [License & Contact](#license--contact)

---

## Overview
This project combines object detection and metric learning to automate signature discovery and authentication from PDFs. It is useful for digitizing and verifying signed documents in workflows such as contracts, forms, and official records.

Key goals:
- Detect signature regions anywhere in a PDF page (using YOLO).
- Extract signature images, preprocess them, and compute similarity between pairs using a Siamese network.
- Provide a human-friendly score and decision threshold for verification.

---

## Highlights
- 🔍 PDF → signature region detection (YOLO-based)
- 🪄 Siamese network for similarity-based signature verification
- 📁 Batch processing or single-document interactive workflows
- ⚖️ Score-based verification with configurable thresholds
- 🧩 Extensible: swap detectors or verification models as needed

---

## How it works
1. PDF Parsing: convert each PDF page to images (rasterization) and feed into the detector.
2. Signature Detection: a YOLO-style object detector finds bounding boxes labelled as "signature".
3. Crop & Preprocess: detected boxes are cropped, normalized, and resized for the verification model.
4. Feature Extraction: the Siamese network generates embeddings for each crop.
5. Similarity Scoring: compute distance (cosine/EUclidean) between embeddings to produce a similarity score.
6. Decision: apply a threshold to decide if two signatures belong to the same signer.

Flow diagram (conceptual):
PDF → page images → YOLO detector → signature crops → Siamese model → similarity score → accept/reject

---

## Quick Start

1. Clone the repository
   ```
   git clone https://github.com/kaifulimaan/Signature-Detection-and-Verification-from-PDF-documents.git
   cd Signature-Detection-and-Verification-from-PDF-documents
   ```

2. Create a virtual environment and install dependencies
   ```
   python -m venv venv
   source venv/bin/activate   # Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. Prepare models
   - Place your trained YOLO weights (e.g., yolov5s.pt or custom) into the models/ directory.
   - Place the Siamese verification checkpoint into models/siamese/.

4. Run detection on a PDF (example)
   ```
   python detect_signatures.py --input documents/example.pdf --output output/
   ```

5. Verify a signature pair (example)
   ```
   python verify_signature.py --ref reference/sign1.png --query output/page1_sig2.png
   ```

(Adapt script names and CLI flags to the repository's actual scripts.)

---

## Usage Example (conceptual)
- Upload a PDF via a simple UI or call a command-line script.
- The detector returns bounding boxes and cropped images stored under `output/`.
- Select a reference signature (trusted sample) and compare against target crops.
- The verifier returns a similarity score (e.g., 0.0–1.0) and a pass/fail decision using a threshold (e.g., 0.5).

Example output:
```
Detected signatures: 4
Comparing reference: ref/sign_A.png -> page1_sig_03.png
Similarity score: 0.82  => MATCH
```

---

## Model Details & Recommendations
- Detector: YOLO-family models (YOLOv5 / YOLOv8 or similar) work well for detecting small, irregular shapes like signatures.
- Verifier: Siamese network with a CNN backbone (e.g., ResNet-lite or MobileNet) trained with contrastive/triplet loss.
- Distance metric: cosine similarity or Euclidean distance on L2-normalized embeddings.
- Threshold: choose per dataset using a validation set (ROC curve/EER) — thresholds transfer poorly across domains without calibration.

Training tips:
- Augment signature crops with small rotations, noise, and varied brightness/contrast.
- Use hard-negative mining for the Siamese network (include visually similar non-matching signatures).

---

## Tips for best results
- Ensure high-quality scans (300 DPI recommended) when converting PDF pages to images.
- Crop margins carefully — include the full signature shape without excessive background.
- Fine-tune detection model on your domain (pen vs digital signatures, stamps, initials).
- Re-calibrate verification threshold if you change pre-processing or model architecture.

---

## Project structure (overview)
A simple layout you might see:
```
/
├─ models/                 # detector & siamese model checkpoints
├─ data/                   # sample PDFs, annotated images
├─ scripts/                # helper scripts for training / inference
├─ detect_signatures.py    # runs detector on PDFs
├─ verify_signature.py     # runs siamese verification on image pairs
├─ requirements.txt
└─ README.md
```

---

## Contributing
Contributions, bug reports, and feature requests are welcome. A good starting point:
- Open an issue to discuss features or bugs.
- Fork, create a branch, and submit a PR with clear description and tests where applicable.
- If adding models, include training config and evaluation scripts to reproduce results.

---

## License & Contact
This project is open for modification — add a LICENSE file for your preferred license (MIT/Apache/GPL).  
For questions or collaboration: open an issue or reach out via GitHub ([@kaifulimaan](https://github.com/kaifulimaan)).

---

Thank you for checking out the project — let me know if you'd like:
- A shorter/simpler README variant
- A README with specific usage commands adapted to existing scripts in the repo
- I can also open a PR to apply this README directly to the repository
