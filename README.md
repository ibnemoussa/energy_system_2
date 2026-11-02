<div align="center">

# AI Energy Forecasting System

An end-to-end solar photovoltaic energy forecasting platform powered by Artificial Neural Networks (ANN) and real-time numerical weather prediction.

[![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-Keras_3.13-FF6F00?style=flat&logo=tensorflow&logoColor=white)](https://www.tensorflow.org/)
[![Django](https://img.shields.io/badge/Django-6.0-092E20?style=flat&logo=django&logoColor=white)](https://www.djangoproject.com/)
[![Django REST Framework](https://img.shields.io/badge/DRF-3.15-red?style=flat&logo=django&logoColor=white)](https://www.django-rest-framework.org/)
[![React](https://img.shields.io/badge/React-19-61DAFB?style=flat&logo=react&logoColor=black)](https://react.dev/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-v3-38B2AC?style=flat&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![Vite](https://img.shields.io/badge/Vite-7-646CFF?style=flat&logo=vite&logoColor=white)](https://vitejs.dev/)

<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#model-selection--deployment-note">Model Selection Note</a> •
  <a href="#system-architecture">System Architecture</a> •
  <a href="#tech-stack">Tech Stack</a> •
  <a href="#project-structure">Project Structure</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#api-reference">API Reference</a> •
  <a href="#troubleshooting">Troubleshooting</a>
</p>

</div>

---

## Overview

The **AI Energy Forecasting System** is a full-stack web platform engineered to deliver short-term solar photovoltaic (PV) generation forecasts at 10-minute intervals for **Kigali, Rwanda** (Latitude: -2.0261, Longitude: 30.3772).

Key operational highlights include:

- **High-Resolution Inference**: Fetches 7-day hourly numerical weather predictions from the Open-Meteo API and interpolates irradiance, temperature, humidity, and wind speed into 10-minute intervals for granular power generation predictions.
- **Trained Deep Learning Engine**: Leverages a pre-trained Keras Artificial Neural Network (`ann_standard_model.keras`) with `StandardScaler` normalization to output solar power (kW) and aggregate hourly or daily energy totals (kWh).
- **Multilingual Modern Interface**: Features a responsive, animated dashboard built with React 19, Framer Motion, and Tailwind CSS with instant translation across **English**, **French (Français)**, and **Arabic (العربية)**.
- **Data Export & Analysis**: Interactive charts with Recharts, summary metric cards, paginated weather/power breakdown tables, and multilingual CSV export capability.

---

## Model Selection & Deployment Note

> **Academic Background & Context:**  
> This application was developed as part of a Master's research dissertation in Renewable Energy Engineering at the University of Rwanda.

The original dissertation evaluated several artificial intelligence paradigms for short-term solar PV forecasting. In the initial empirical evaluations, **Random Forest (RF)** demonstrated the strongest overall statistical accuracy metrics across the benchmark test suite and was initially designated for software prototype development.

However, during end-to-end integration and runtime deployment testing:
- **Random Forest Behavior**: Occasional small, non-zero energy values were predicted during night hours when solar irradiance was zero.
- **Artificial Neural Network (ANN) Behavior**: The trained ANN model consistently produced true zero nighttime energy predictions under identical zero-irradiance conditions, matching physical PV system behavior.

Consequently, the **ANN-based pipeline** was selected and retained for the production application demonstrated during the Master's thesis defense to enforce strict physical realism.

**Important clarification:** The selection of ANN for the operational deployment was driven by runtime behavioral constraints (zero nighttime generation) observed in live testing, not by higher overall statistical metrics in the dissertation's offline evaluation. The historical dissertation conclusions remain unchanged, while this repository houses the active ANN deployment.

---

## System Architecture

```text
+-------------------------------------------------------------------------+
|                           Open-Meteo API                                |
|  Fetches 7-day hourly forecasts: shortwave radiation, temperature,      |
|  relative humidity, and wind speed for Kigali, Rwanda                   |
+------------------------------------+------------------------------------+
                                     |
                                     | HTTP REST (JSON)
                                     v
+-------------------------------------------------------------------------+
|                      Django 6 + DRF Backend (:8000)                     |
|                                                                         |
|  1. Linear interpolation: 1-hour resolution -> 10-minute time steps     |
|  2. Feature Scaling: scikit-learn StandardScaler (standard_scaler.pkl)  |
|  3. Model Inference: Keras 3.13 ANN (ann_standard_model.keras)          |
|  4. Aggregation: 10-min kW -> Hourly / Daily Energy in kWh (Delta T)    |
|  5. Serialization: Formats energy curves & meteorological summaries     |
+------------------------------------+------------------------------------+
                                     |
                                     | JSON Response (POST /api/forecast/run/)
                                     v
+-------------------------------------------------------------------------+
|                     React 19 + Vite 7 Frontend (:5173)                  |
|                                                                         |
|  - Real-time Interactive Recharts (Hourly & Daily energy profiles)      |
|  - Weather & Generation Paginated Data Table with sorting               |
|  - Trilingual Support (English / Francais / Arabic RTL)                 |
|  - Client-side CSV export with localized column headers                 |
+-------------------------------------------------------------------------+
```

---

## Tech Stack

| Layer | Technology | Version | Purpose / Responsibilities |
| --- | --- | --- | --- |
| **Frontend Framework** | React | 19.2.0 | Reactive component architecture and UI state management |
| **Build Tool** | Vite | 7.3.1 | High-speed frontend bundling, HMR, and development server |
| **Styling** | Tailwind CSS | 3.4.19 | Utility-first responsive design, dark-mode styling, transitions |
| **Charts & Graphics** | Recharts | 3.7.0 | Interactive solar generation curves and data visualization |
| **UI Motion** | Framer Motion | 12.34.3 | Smooth UI transitions, card reveals, and loading states |
| **Icons** | Lucide React | 0.575.0 | Clean iconography across dashboards, buttons, and badges |
| **Backend Framework** | Django | 6.0+ | Server application scaffolding, routing, and CORS handling |
| **REST API** | Django REST Framework | 3.15+ | API views, request payload validation, and JSON responses |
| **Deep Learning** | TensorFlow / Keras | 3.13.2 | Neural network inference via `ann_standard_model.keras` |
| **Data Processing** | pandas & NumPy | Latest | 10-minute interpolation, time-series resampling, array math |
| **Feature Normalization** | scikit-learn | 1.7.2 | `StandardScaler` feature transform across 4 input features |
| **Model Serialization** | joblib | Latest | Persisted scaler and feature list pipeline loading |
| **External Weather** | Open-Meteo API | v1 (Free) | Hourly shortwave radiation, 2m temp, humidity, wind speed |

---

## Project Structure

```text
AI_Energy_System/
├── ANN/
│   ├── models/
│   │   ├── ann_features.pkl           # Ordered list of training features
│   │   ├── ann_standard_model.keras   # Trained Keras Artificial Neural Network
│   │   └── standard_scaler.pkl        # Pre-fitted scikit-learn StandardScaler
│   └── ANN_Scaled.ipynb               # Model training & experimentation notebook
├── backend/
│   ├── energy_project/
│   │   ├── settings.py                # Django project settings & CORS configuration
│   │   ├── urls.py                    # Root URL routing
│   │   └── wsgi.py                    # WSGI gateway interface
│   ├── forecast/
│   │   ├── services/
│   │   │   └── ann_service.py         # Open-Meteo fetch, interpolation & inference
│   │   ├── urls.py                    # Endpoint mapping for /api/forecast/
│   │   └── views.py                   # API controller for forecast execution
│   ├── manage.py                      # Django administrative CLI
│   ├── requirements.txt               # Backend Python dependencies
│   └── run_server.bat                 # Windows launch helper script
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── ui/                    # Reusable Button, Card, and Input components
│   │   │   └── EnergyForecastDashboard.jsx  # Main interactive dashboard & translations
│   │   ├── App.jsx                    # Root application component
│   │   ├── index.css                  # Tailwind styles and base font definitions
│   │   └── main.jsx                   # React 19 application bootstrap
│   ├── index.html                     # HTML5 template entry
│   ├── package.json                   # Frontend npm dependencies and scripts
│   ├── tailwind.config.js             # Tailwind CSS theme & plugin configuration
│   └── vite.config.js                 # Vite bundler configuration
├── start.sh                           # One-click macOS environment and launch script
└── README.md                          # Repository documentation
```

---

## Quick Start

### Prerequisites

Ensure you have the following installed on your machine:

1. **Conda (Miniconda or Anaconda)**: Python environment manager.  
   Download from [Miniconda Documentation](https://docs.conda.io/en/latest/miniconda.html).
2. **Node.js (v18 or newer)**: JavaScript runtime and npm.  
   Download from [Node.js Official Website](https://nodejs.org/) or install via Homebrew:
   ```bash
   brew install node
   ```

---

### One-Click Automated Startup (macOS)

Run the root automation script from the repository root:

```bash
bash start.sh
```

**First-Time Execution:**
- Checks for Conda and creates the dedicated `ML_2` environment with Python 3.12.
- Installs Python packages listed in `backend/requirements.txt`.
- Runs `npm install` inside `frontend/` to configure JavaScript packages.

**Subsequent Runs:**
- Detects the prepared `ML_2` environment and dependencies, bypassing installation.
- Spawns two independent Terminal windows:
  - **Backend Server**: Django runs on `http://localhost:8000`.
  - **Frontend Server**: Vite runs on `http://localhost:5173`.
- Automatically opens `http://localhost:5173` in your default browser.

> **Shutting Down:** Press `Ctrl + C` in each Terminal window or close them.

---

### Hardware Acceleration Note (Apple Silicon M1 / M2 / M3 / M4)

For optimal execution on Apple Silicon with Metal GPU acceleration, replace `tensorflow-cpu` with `tensorflow-macos` and `tensorflow-metal` in `backend/requirements.txt`:

```text
tensorflow-macos
tensorflow-metal
```

---

### Manual Startup (Cross-Platform)

If you prefer running services manually or are on Linux / Windows:

#### 1. Backend Service

```bash
# Activate the Conda environment
conda activate ML_2

# Navigate to backend directory
cd backend

# Run the Django development server
python manage.py runserver 8000
```

The Django REST API will be accessible at `http://localhost:8000`.

#### 2. Frontend Application

Open a separate terminal shell:

```bash
# Navigate to frontend directory
cd frontend

# Install packages (first time only)
npm install

# Start Vite dev server
npm run dev
```

Open `http://localhost:5173` in your browser.

---

## Dashboard Capabilities

| Feature | Details |
| --- | --- |
| **Single Day Forecast** | Select any single date within 7 days to view a detailed 24-hour solar power generation profile (kW and kWh). |
| **Date Range Forecast** | Select a range up to 7 days to project cumulative daily solar generation trends. |
| **Quick Date Selectors** | One-click presets for **Today**, **Tomorrow**, and **Day After** to rapidly generate forecasts. |
| **Trilingual UI** | Instant localized text switching between **English**, **French (Français)**, and **Arabic (العربية)**. |
| **Interactive Charting** | Dynamic line chart plotted with Recharts, with custom hover tooltips showing time and energy output. |
| **Detailed Weather Table** | Tabular overview with pagination (10 rows/page) showing temperature, humidity, solar irradiance, wind speed, and energy. |
| **CSV Export** | Export the current forecast to CSV with column headers localized in the active language. |

---

## API Reference

### Run Forecast

Performs real-time weather retrieval, data interpolation, neural network inference, and energy aggregation.

- **URL:** `/api/forecast/run/`
- **Method:** `POST`
- **Headers:** `Content-Type: application/json`

#### Single Day Request Payload

```json
{
  "mode": "single",
  "single_date": "2026-03-06"
}
```

#### Date Range Request Payload

```json
{
  "mode": "range",
  "start_date": "2026-03-06",
  "end_date": "2026-03-10"
}
```

#### Response Example (`200 OK`)

```json
{
  "energy_data": [
    {
      "label": "06:00",
      "energy": 0.0000
    },
    {
      "label": "07:00",
      "energy": 0.0412
    },
    {
      "label": "08:00",
      "energy": 0.1234
    },
    {
      "label": "12:00",
      "energy": 0.8521
    }
  ],
  "weather_summary": [
    {
      "label": "08:00",
      "temperature": 22.5,
      "humidity": 65.0,
      "irradiance": 450.3,
      "wind": 12.1,
      "energy": 0.1234
    },
    {
      "label": "12:00",
      "temperature": 27.8,
      "humidity": 51.2,
      "irradiance": 892.4,
      "wind": 14.5,
      "energy": 0.8521
    }
  ]
}
```

#### Error Response Example (`400 Bad Request`)

```json
{
  "error": "No weather data for the selected date(s). Open-Meteo provides up to 7 days of forecast from today."
}
```

---

## Troubleshooting

| Symptom / Issue | Underlying Cause | Solution |
| --- | --- | --- |
| `ModuleNotFoundError: No module named 'tensorflow'` | Dependencies were not installed in the active environment | Re-run `bash start.sh` or run `pip install -r backend/requirements.txt` inside the `ML_2` environment. |
| `conda: command not found` | Conda is not configured in current terminal profile | Run `source ~/miniconda3/etc/profile.d/conda.sh` (or your Anaconda path), or restart your terminal shell. |
| Conda environment `ML_2` not found | Initial environment creation failed or was skipped | Execute `conda create -n ML_2 python=3.12 -y` and rerun `bash start.sh`. |
| `CORS error` or network error in frontend | Backend is stopped or not listening on port 8000 | Verify the backend server is running at `http://localhost:8000` and `energy_project/settings.py` includes CORS headers. |
| `No data returned` from Open-Meteo | Selected date is outside the 7-day weather forecast limit | Select dates between today and the next 7 calendar days. |
| `npm: command not found` | Node.js runtime is missing | Install Node.js v18+ from [nodejs.org](https://nodejs.org/) or run `brew install node`. |
| `Port 8000 already in use` | Zombie Django process or port collision | Terminate the process using: `lsof -ti:8000 \| xargs kill -9` and relaunch. |
| `Port 5173 already in use` | Vite dev server already active in another session | Terminate the process using: `lsof -ti:5173 \| xargs kill -9` and relaunch. |

---

## License & Academic Disclaimer

Developed for academic research at the University of Rwanda, Master's Program in Renewable Energy Engineering. All research citations and baseline evaluations refer to the original thesis benchmarks.
