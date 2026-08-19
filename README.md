# Pepper in Healthcare: Reasoning, Recovery and Benchmarking in a Pharmacy Context

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Platform](https://img.shields.io/badge/platform-SoftBank%20Pepper%20%7C%20NAOqi-green.svg)](https://www.ald.softbankrobotics.com/)
[![LLM](https://img.shields.io/badge/LLM-Mistral%20Large-orange.svg)](https://mistral.ai/)
[![Docker](https://img.shields.io/badge/docker-ready-blue.svg)](https://www.docker.com/)

An autonomous, socially intelligent robotic assistant based on SoftBank Robotics' **Pepper** platform, designed to operate in a community pharmacy environment. The system acts as an intelligent first-level triage assistant providing multilingual drug advice, prescription/health card validation, GDPR-compliant face recognition onboarding, and robust error recovery mechanisms.

---

## 📌 Key Features

1. **Multimodal Social Perception & Onboarding (HRI):**
   * **Face Recognition:** Real-time identification using `dlib` and `face_recognition` with temporal filtering (`UNKNOWN_FACE_THRESHOLD`) to avoid false positives.
   * **Ethical Consent Management (GDPR):** Proactive verbal & visual onboarding for unknown customers, with explicit opt-in consent for biometric storage and instant data deletion support.

2. **Adaptive LLM Dialogue & Reasoning:**
   * Powered by **Mistral Large** (`mistral-large-latest`) with stateless prompt engineering injecting the local pharmaceutical database (`database.json`).
   * Structured JSON parsing returning semantic commands (`action_type`), enabling symptom-based OTC recommendations, out-of-stock alternative suggestions, and confirmation loops.

3. **Simulated Regulatory Document Verification:**
   * Barcode (Italian Health Card / Codice Fiscale) and QR Code (Electronic Medical Prescription) scanning via `pyzbar` and `OpenCV`.
   * **Cascade Checks:** Expiration date validation and consistency matching between the prescription's owner and the Health Card.
   * **Tolerance & Assisted Recovery:** Contextual retry loop with non-punitive social cues and gestural feedback on mismatch.

4. **Robust Benchmarking Pipeline (RBC):**
   * Validated under the RBC evaluation framework with a **Final Task Pipeline Score ($Score_{FTPS}$)** of **0.894** (target threshold: $\ge 0.70$).

---

## 🏛️ System Architecture

<p align="center">
  <img width="1600" height="675" alt="Code_Generated_Image" src="https://github.com/user-attachments/assets/4895c866-7dd0-4b0a-9b6b-af3c1961be89" />
</p>

The project relies on a distributed microservices architecture coordinated by the **Rich Adaptive Interaction Manager (RAIM)** middleware:
* **User Interface (Frontend):** Runs on Pepper's tablet browser, handling speech capture via the Web Speech API and visual feedback.
* **Intelligent Processing (LLM):** Python 3 server interfacing with **Mistral Large** for contextual reasoning and intent classification.
* **Perception & Scanning:** Dedicated Python 3 services using `face_recognition` (social identity) and `pyzbar` / `OpenCV` (barcode/QR verification).
* **Robot Control (PepperBot):** Core Python 2.7 / NAOqi bridge executing physical gestures and speech synthesis.

---

## 📂 Repository Structure

```text
.
├── hri_software/               # Submodule: Base HRI Docker & software environment
├── server_docker/              # Docker environment configuration and runtime scripts
│   ├── Dockerfile
│   ├── build
│   ├── run
│   ├── run_nginx
│   └── dc_nvidia / dc_vnc / dc_x11
├── playground/                 # Core application code & backends
│   ├── server_face_recognition.py   # Facial recognition service (dlib/face_recognition)
│   ├── server_pepperbot.py          # PepperBot robot interface & actuation
│   ├── server_pharmacy_interaction.py # Mistral Large dialogue & semantic reasoning
│   ├── server_scanning.py           # Document verification service (pyzbar/OpenCV)
│   ├── FaceRecognition/             # Encodings and face management assets
│   ├── pepperbot/                   # Robot movement and behavior modules
│   ├── Products/                    # Medication catalog and metadata
│   └── RAIM/                        # Middleware communication orchestration
├── src/Pepper/                 # Submodules
│   ├── modim/                       # Tablet multimodal interaction manager
│   └── pepper_tools/                # Pepper execution tools
├── architecture.jpg            # System architecture diagram
├── Report.pdf                  # Full academic report and experimental evaluation
└── README.md                   # Project documentation
```

---

## 🚀 Getting Started

### 1. Prerequisites
* **Docker** & **Docker Compose**
* **Python 3.8+** (for local server execution if running outside Docker)
* Access to a **Mistral AI API Key**
* Webcam and Microphone (for Web Speech API and video streaming)

### 2. Clone the Repository with Submodules
```bash
git clone --recurse-submodules https://github.com/giadap00/Pepper_in_Pharmacy.git
cd Pepper_in_Pharmacy
```
*If already cloned without submodules:*
```bash
git submodule update --init --recursive
```

### 3. Environment Configuration
Export your Mistral API key or configure it in the relevant server config:
```bash
export MISTRAL_API_KEY="your_mistral_api_key_here"
```

### 4. Running with Docker
Build and launch the dedicated container environment:
```bash
cd server_docker
./build
./run
```

### 5. Running Backends Manually
Alternatively, launch the Python 3 microservices from the `playground/` directory:
```bash
# 1. Face Recognition Server
python3 server_face_recognition.py

# 2. Document Scanning Server
python3 server_scanning.py

# 3. Pharmacy Reasoning Server (Mistral Large)
python3 server_pharmacy_interaction.py

# 4. PepperBot Core Server
python server_pepperbot.py
```
Open the web application on Pepper's tablet browser to initialize the frontend and connect via RAIM.

---

## 📊 Benchmark & Evaluation Results

Evaluated following the **Robot Benchmarking and Competitions (RBC)** methodology across 4 weighted modules:

$$Score_{FTPS} = \sum_{i=1}^{4} w_i \cdot M_i = 0.894 \quad (Threshold \ge 0.70)$$

| Module ($M_i$) | Metric | Weight ($w_i$) | Value | Weighted Score ($w_i \cdot M_i$) |
| :--- | :--- | :---: | :---: | :---: |
| **ASR Accuracy ($M_1$)** | Word Error Rate (WER) | 0.20 | 67.6% ($WER = 32.4\%$) | **0.135** |
| **Social Accuracy ($M_2$)** | Face Recognition Accuracy | 0.15 | 96.5% | **0.145** |
| **Planning & Reasoning ($M_3$)** | Action Accuracy | 0.35 | 100% | **0.350** |
| **Regulatory Accuracy ($M_4$)** | Recovery Success Rate | 0.30 | 88.0% | **0.264** |
| **Final Task Pipeline Score** | $Score_{FTPS}$ | **1.00** | **—** | **0.894 / 1.00** |

---

## 📄 License & Academic Citation
Developed for the **HRI & RBC 2025/2026** course at Sapienza University of Rome. Refer to `Report.pdf` for the complete theoretical framework and experimental user study design.
