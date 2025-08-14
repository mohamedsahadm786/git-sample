# 🎙️ AI Audio Interview Coach

An end-to-end **audio interview training** tool that:
- Generates **context-aware questions** (uses your Job Title, Job Description, Résumé, Experience Level).
- Lets you **answer by voice** (Malayalam / English / Kannada).
- **Cleans & enhances audio** (noise reduction → WPE dereverb → band-pass → LUFS normalization → *(optional)* deep speech enhancement).
- **Detects language** (Whisper) and **transcribes** with OpenAI (preserves filler words, hesitations, `...` pauses).
- **Translates** non-English answers to English for comparable feedback.
- **Analyzes delivery** (WPM, filler ratio, long pauses via MFA alignment) and provides **AI feedback** by comparing to a model answer.
- Supports **Live mode** (continuous Q&A) and **Recorded mode** (retry takes and keep the best).

---

## 📚 Table of Contents
- [Overview](#-overview)
- [Features](#-features)
- [Requirements](#-requirements)
  - [1) Python & OS](#1-python--os)
  - [2) Python Packages](#2-python-packages)
  - [3) External Tools](#3-external-tools)
  - [4) Environment Variables / PATH](#4-environment-variables--path)
  - [5) API Keys](#5-api-keys)
- [Download the Code](#-download-the-code)
- [Local Setup (Step-by-Step)](#-local-setup-step-by-step)
- [How to Run](#-how-to-run)
- [Config You May Need to Edit](#-config-you-may-need-to-edit)
- [Outputs](#-outputs)
- [Optional: MFA Alignment](#-optional-mfa-alignment)
- [Troubleshooting](#-troubleshooting)
- [Security Note](#-security-note)
- [Sample requirements.txt](#-sample-requirementstxt)
- [License](#-license)

---

## 🔎 Overview
This project implements a **single-notebook pipeline** that simulates realistic interview sessions. It asks **increasingly challenging** questions tailored to your context, records answers from your **microphone**, runs a **robust audio preprocessing chain**, performs **language detection + exact transcription** (preserving fillers & pauses), optionally **translates** to English, and computes **speaking-delivery metrics** (WPM, filler ratio, pause structure). It then provides **structured AI feedback** by comparing your answer to a generated **reference answer**.

---

## ✨ Features
- **Languages:** `en`, `ml`, `kn`  
  (Whisper for language ID + GPT verification fallback)
- **Audio pipeline:** `noisereduce` → **WPE dereverb** (`nara_wpe`) → **band-pass** (≈300–3400 Hz) → **loudness normalization** (≈ −23 LUFS) → *(optional)* **DL enhancement** (Torch model via `hyperpyyaml`)
- **Transcription:** `gpt-4o-transcribe` (preserves fillers & pauses)
- **Translation:** `deep-translator` (Malayalam/Kannada → English)
- **TTS (optional):** `pyttsx3` can read questions/model answers aloud
- **Résumé parsing:** `PyMuPDF (fitz)` reads PDF and injects context
- **Analytics:** speaking rate (WPM), filler ratio/count, pause counts/durations (via **MFA JSON**)
- **Modes:** **Live** (continuous Q&A) and **Recorded** (re-record per question)
- **Persistence:** session history saved to `history.json`

---

## ✅ Requirements

### 1) Python & OS
- **Python:** 3.9 – 3.11 recommended  
- **OS:** Windows / macOS / Linux  
- **Microphone:** required (`sounddevice` / PortAudio)  
- **GPU (optional):** for Torch/Whisper speedups (install CUDA-matching wheels)

### 2) Python Packages
Create a virtual environment and install dependencies:

```bash
# Create and activate (choose one)
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

# Upgrade pip
pip install -U pip

# PyTorch/torchaudio (CPU baseline; see https://pytorch.org for CUDA builds)
pip install torch torchaudio --index-url https://download.pytorch.org/whl/cpu

# Core packages
pip install -U \
  openai-whisper openai sounddevice scipy numpy pyttsx3 librosa pydub \
  pymupdf deep-translator soundfile noisereduce pyloudnorm nara_wpe \
  torchaudio hyperpyyaml ipython
'''
dff
---
