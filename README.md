# Elite--hit
Hospital Management using AI
<div align="center">

<h1>SymptoSense AI</h1>

<p><strong>Agentic patient triage, autonomous appointment scheduling, and urban health intelligence — fully offline, no external APIs required.</strong></p>

[![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![Flask](https://img.shields.io/badge/Flask-latest-000000?style=flat&logo=flask&logoColor=white)](https://flask.palletsprojects.com)
[![SQLite](https://img.shields.io/badge/SQLite-SQLAlchemy-003B57?style=flat&logo=sqlite&logoColor=white)](https://www.sqlalchemy.org)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-F7931E?style=flat&logo=scikitlearn&logoColor=white)](https://scikit-learn.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat)](LICENSE)
[![No APIs](https://img.shields.io/badge/External%20APIs-None-brightgreen?style=flat)](#)

</div>

---

## Overview

**SymptoSense AI** is a full-stack autonomous healthcare web application built with Flask and SQLAlchemy. It combines rule-based NLP symptom analysis, automated appointment scheduling, real-time disease tracking, and AI-assisted nutritional guidance — all running entirely on-device with no cloud dependencies.

The system acts as an intelligent triage agent: a patient describes their symptoms, the NLP engine determines urgency and the appropriate medical department, and the scheduler autonomously books the next available appointment with a suitable doctor. Clinicians and administrators can monitor hospital capacity, disease trends, and medical facility availability through a live dashboard.

---

## Features

### 🩺 Symptom Triage & NLP Analysis
- Rule-based NLP engine classifies symptoms into **High**, **Medium**, or **Low** urgency
- Maps symptoms to the correct medical department (Cardiology, Pulmonology, Neurology, Orthopedics, General)
- Handles typos and fuzzy input via `difflib` sequence matching
- No third-party NLP APIs — all logic runs locally in `nlp.py`

### 📅 Autonomous Appointment Scheduling
- Automatically selects an available doctor matching the suggested department
- Assigns time slots based on urgency level (High → 09:00, Medium → 10:00, Low → 11:00)
- Finds the next conflict-free slot in 10-minute increments
- Enforces a daily cap of 30 appointments per day
- Falls back to the next available day if the selected date is fully booked

### 🏥 Urban Hospital Intelligence
- Manages a network of **25 sample hospitals** with doctor rosters and specialisation coverage
- Tracks **doctor shortages and excess** per specialty across hospitals
- Lists medical facilities (MRI, ICU, CT Scan, Pharmacy, Blood Bank, etc.) per hospital
- Disease occurrence tracker: records and increments case counts per condition per city

### 📊 Disease Tracker
- Live tracking of: Flu, Diabetes, Hypertension, COVID-19, Asthma, Malaria
- Case counts increment automatically when a matching symptom is submitted
- Fuzzy keyword matching catches common misspellings (e.g. "diabetis", "covd")
- Dashboard chart renders real-time disease statistics

### 🤖 AI Chatbot (No External LLM)
- Embedded chatbot answers navigation questions about every feature in the app
- Detects symptom-related queries and returns urgency + department suggestions
- Fuzzy phrase matching for robust intent recognition
- Provides randomised general health tips on demand
- Fallback response for unrecognised inputs

### 🥗 AI Nutritionist
- Personalised dietary advice based on **age**, **gender**, **BMI**, and **dietary preference**
- Covers vegan, vegetarian, and non-vegetarian diets
- BMI-based guidance (underweight → obese)
- Age-group and gender-specific recommendations (iron for premenopausal women, heart-healthy fats for men, calcium for seniors)

### 🖼️ Medical Image Analysis
- Local pixel-level image analysis via NumPy and Pillow
- Detects grayscale medical scans (X-ray, CT) and classifies brightness levels
- Identifies dominant colour channels for preliminary scan categorisation
- No radiological AI API required

### 🔐 User Authentication
- Secure signup and login with password hashing via Werkzeug
- Session-based access control with `@login_required` decorator
- Email uniqueness validation and minimum password length enforcement
- Persistent user store via SQLite (`users` table)

---

## Architecture

```
SymptoSense AI/
├── app.py                  # Flask application, all routes and API endpoints
├── db.py                   # SQLAlchemy db instance
├── models.py               # Core models: User, Patient, Doctor, Appointment, PatientHistory
├── models_urban.py         # Urban models: Hospital, UrbanDoctor, DiseaseOccurrence, MedicalFacility
├── nlp.py                  # Symptom NLP engine (urgency + department classification)
├── scheduler.py            # Autonomous appointment scheduling logic
├── image_ai.py             # Local medical image analysis (NumPy + Pillow)
├── requirements.txt
│
├── templates/
│   ├── index.html          # Main dashboard (symptom checker, appointments, disease tracker)
│   ├── login.html          # Authentication — login
│   └── signup.html         # Authentication — signup
│
└── static/
    └── uploads/            # Uploaded medical images
```

**Request flow:**

```
Browser (User)
      │
      ▼
  Flask (app.py)
      │
      ├──► nlp.py            → Urgency level + Department
      │
      ├──► scheduler.py      → Doctor selection + Time slot assignment
      │
      ├──► models_urban.py   → Hospital / Disease / Facility queries
      │
      ├──► image_ai.py       → Medical image classification
      │
      └──► SQLite DB         → Persist patients, appointments, history, users
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Web Framework | Flask |
| ORM / Database | Flask-SQLAlchemy, SQLite |
| NLP & Triage | Custom rule-based engine (`nlp.py`) |
| Image Analysis | Pillow, NumPy |
| ML Support | scikit-learn |
| Auth | Werkzeug (PBKDF2 password hashing) |
| Frontend | HTML5, CSS3, JavaScript (vanilla) |

> **No external AI APIs are used.** All intelligence runs locally on your machine.

---

## Getting Started

### Prerequisites

- Python **3.8** or higher
- `pip`

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/Tandrimanandy/symptosense-ai.git
cd symptosense-ai

# 2. (Recommended) Create a virtual environment
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # macOS / Linux

# 3. Install dependencies
pip install -r requirements.txt
```

### Running the Application

```bash
python app.py
```

Then open your browser and navigate to:

```
http://localhost:5000
```

On first run, the database is created automatically and populated with:
- 25 sample hospitals with randomised medical facilities
- A roster of doctors across 10 specialities
- Seed disease occurrence data for 6 conditions

---

## Usage Guide

### 1. Create an Account
Navigate to `/signup`, enter your email and a password (minimum 6 characters), and register.

### 2. Submit Symptoms
On the main dashboard, enter your name, describe your symptoms, and select a preferred date. The system will:
- Classify urgency (High / Medium / Low)
- Suggest a medical department
- Automatically book an appointment with an available specialist

### 3. View Appointments
Browse all scheduled appointments, filterable by date. Each record shows patient ID, assigned doctor, time slot, hospital, and status.

### 4. Patient History
Look up any patient by ID to retrieve their full symptom and appointment history in chronological order.

### 5. Hospital & Doctor Directory
The **Hospitals** tab shows all 25 hospitals with doctor counts per speciality. The **Doctors** tab lists all registered physicians with their speciality and affiliated hospital.

### 6. Disease Tracker
Charts update in real time as new symptom submissions are logged. Track case volumes for Flu, Diabetes, Hypertension, COVID-19, Asthma, and Malaria.

### 7. AI Nutritionist
Submit age, gender, BMI, and dietary preference to receive a personalised, multi-factor nutritional recommendation.

### 8. Chatbot
Use the embedded chatbot to:
- Navigate the app ("how do I book an appointment?")
- Analyse symptoms ("I have chest pain and shortness of breath")
- Get general health tips ("give me a health tip")

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/signup` | Register a new user |
| `POST` | `/login` | Authenticate and create a session |
| `GET` | `/logout` | Clear the session |
| `POST` | `/submit_symptoms` | Triage symptoms and schedule an appointment |
| `GET` | `/appointments` | List all appointments (optionally filter by `?date=`) |
| `GET` | `/history/<patient_id>` | Retrieve a patient's full history |
| `GET` | `/doctors` | List all doctors with speciality and hospital |
| `GET` | `/urban/hospitals` | Hospital specialisation and doctor counts |
| `GET` | `/urban/diseases` | Disease occurrence counts (real-time) |
| `GET` | `/medical_facilities` | Facilities available per hospital |
| `POST` | `/nutritionist` | Generate personalised dietary advice |
| `POST` | `/chatbot` | Query the embedded AI assistant |

---

## Database Schema

```
users              patients           doctors
──────────         ──────────         ──────────
id (PK)            patient_id (PK)    doctor_id (PK)
email              name               name
password_hash      created_at         specialty
created_at                            room_number

appointments       patient_history    hospital
──────────────     ───────────────    ──────────
appointment_id     history_id (PK)    id (PK)
patient_id (FK)    patient_id (FK)    name
doctor_id (FK)     symptoms_reported  location
day                report_date
time_slot          urban_doctor       disease_occurrence
status             ────────────       ──────────────────
urgency_level      id (PK)            id (PK)
                   name               disease_name
                   specialty          count
                   hospital_id (FK)   city
```

---

## System Requirements

| Requirement | Minimum |
|-------------|---------|
| OS | Windows 10 / macOS 11 / Ubuntu 20.04 |
| Python | 3.8+ |
| RAM | 2 GB |
| Storage | 200 MB |

---

## Troubleshooting

**Database errors on first run**
The database is created automatically. If you see table errors, delete `symptosense.db` and restart the app.

**Missing dependencies**
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

**Port already in use**
Change the port by editing the last line of `app.py`:
```python
app.run(debug=True, port=5001)
```

**Image analysis returning unexpected results**
The image engine uses pixel-level brightness and colour statistics. For clinical-grade analysis, consult a qualified radiologist.

---

## Roadmap

- [ ] REST API with JWT authentication
- [ ] Real ML-based NLP using a lightweight local model (e.g. spaCy)
- [ ] Doctor availability calendar with drag-and-drop scheduling
- [ ] PDF appointment and triage report export
- [ ] Multi-city disease trend comparison
- [ ] Role-based access (Admin, Doctor, Patient)
- [ ] Mobile-responsive UI overhaul

---

## Contributing

Contributions are welcome. To get started:

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes using [Conventional Commits](https://www.conventionalcommits.org/): `git commit -m "feat: add your feature"`
4. Push to the branch: `git push origin feature/your-feature-name`
5. Open a Pull Request describing the change and its motivation.

Please follow [PEP 8](https://pep8.org/) and include docstrings for all new functions and classes.

---

## License

Distributed under the **MIT License**. See [`LICENSE`](LICENSE) for full terms.

---

## Acknowledgements

SymptoSense AI is built on top of outstanding open-source projects:

- [Flask](https://flask.palletsprojects.com/) — lightweight web framework
- [SQLAlchemy](https://www.sqlalchemy.org/) — Python SQL toolkit and ORM
- [Werkzeug](https://werkzeug.palletsprojects.com/) — WSGI utilities and security
- [Pillow](https://python-pillow.org/) — image processing library
- [NumPy](https://numpy.org/) — numerical computing
- [scikit-learn](https://scikit-learn.org/) — machine learning utilities

---

<div align="center">
<sub>Built for smarter, faster, and more private healthcare triage — entirely on your machine.</sub>
</div>
