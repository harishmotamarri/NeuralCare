# NeuralCare

## Short Description
NeuralCare is a smart, predictive health-tech system designed to assist hospitals in forecasting inpatient bed, ICU, and ventilator occupancy, as well as daily patient admissions. By leveraging trained machine learning models, NeuralCare empowers medical administrators to anticipate surges, prevent resource shortages, and optimize resource allocation.

---

## Features
- **Secure Hospital Portal**: Administrative login and session management powered by Supabase.
- **Predictive Dashboard**: Visualizes predictions for general beds, ICU beds, ventilators, and admissions, scaled for different timeframes (daily, weekly, monthly).
- **Resource Shortage Risk Scoring**: Calculates and displays dynamic probabilities of potential critical resource deficits.
- **Real-Time Resource Tracking**: Interface for checking current hospital capacity and status updates.
- **Interactive Metrics & Charts**: Beautiful, responsive user interfaces presenting occupancy metrics and trends.
- **Lazy-Loaded ML Weights**: Automatic download of Scikit-learn model files (`.pkl`) from Hugging Face on the first request to ensure fast builds and light git size.

---

## Tech Stack
- **Backend**: Python, Flask, Flask-CORS, Pandas, NumPy, Scikit-learn, HuggingFace Hub
- **Frontend**: HTML5, Vanilla CSS, Vanilla JavaScript, Supabase JS Client SDK, FontAwesome, Google Fonts
- **Deployment**: Render Web Services, Hugging Face Hub (model storage)

---

## Architecture / Workflow
```
[Hospital Staff] 
      │ 
      ▼ (Logs in & Inputs Metrics)
[Frontend Client] ────(POST JSON request to /predict)────► [Flask Backend (app.py)]
      ▲                                                         │ (On first run)
      │                                                         ▼
[Dynamic Dashboard] ◄───(Returns Predictions JSON)─────── [Hugging Face Hub]
 (Charts & Alerts)                                         (Downloads .pkl weights)
```
1. **Frontend Request**: The administrative user enters current metrics (e.g., total vs. occupied beds/ICU/ventilator units, admissions) on the dashboard page. A POST request is made to the `/predict` API route.
2. **Model Download & Initialization (Lazy Load)**: If the models are not loaded in memory, [app.py]triggers [download_models.py] to download the 8 `.pkl` model files from the Hugging Face repository `harishuuu/neuracare-models`.
3. **Feature Engineering & Inference**: The backend processes input data, derives calendar features (month, week number, day of year, quarter), and runs inferences against the trained models.
4. **Blending Risk Probabilities**: The system calculates shortages using both model probabilities and logistic occupancy-to-probability scaling curves to provide highly accurate alerts.
5. **Dashboard Response**: The predicted resources and shortage alerts are returned as a JSON payload, instantly updating the frontend.

---

## Installation

### Prerequisites
- Python 3.11.x (Recommended: 3.11.9)
- Hugging Face Read API Token (to download model files)

### Setup Steps
1. Clone the repository to your local machine:
   ```bash
   git clone https://github.com/harishmotamarri/NeuralCare.git
   cd NeuralCare-deploy
   ```

2. Create a virtual environment:
   ```bash
   python -m venv venv
   ```

3. Activate the virtual environment:
   - **Windows (PowerShell)**:
     ```powershell
     .\venv\Scripts\Activate.ps1
     ```
   - **macOS/Linux**:
     ```bash
     source venv/bin/activate
     ```

4. Install the required dependencies:
   ```bash
   pip install -r requirements.txt
   ```

5. Set up environment variables:
   Copy the example environment file to `.env`:
   ```bash
   cp .env.example .env
   ```
   Open the `.env` file and insert your Hugging Face API token (`HF_TOKEN`).

6. Start the Flask application server:
   ```bash
   python app.py
   ```
   The application will start, by default listening on `http://127.0.0.1:10000/`.

---

## Usage
1. Open your browser and navigate to `http://127.0.0.1:10000/` to view the landing page.
2. Click **Hospital Login** to sign in to your hospital workspace (credentials validated via Supabase).
3. Access the **Dashboard** to view key metrics and charts.
4. Under the **Predictions** section, input current beds, ICU, and ventilator occupancy numbers, and click the forecast button to view predicted needs and shortage risk meters.
5. Use **Check Availability** to verify the current availability status of other hospitals in the network.

---

## Project Structure
```
NeuralCare/
├── assets/                          # Static UI Assets
│   ├── css/
│   │   └── landing.css              # Main landing page stylesheet
│   ├── img/                         # Images & logos
│   │   ├── CMR-Institute-of-Medical-Science--jpeg.webp
│   │   ├── download.jpg
│   │   └── download_logo.jpg
│   └── js/
│       ├── dashboard.js             # Logic for forecasting & dashboard interactions
│       ├── landing.js               # Landing page scripts
│       └── supabase-client.js       # Supabase initialization client
├── models/                          # Machine learning models (Git ignored)
├── templates/                       # Frontend HTML files
│   ├── checkAvailability.html       # Public check availability page
│   ├── dashboard.html               # Main administrator prediction panel
│   ├── hospitalLogin.html           # Administrator login portal
│   └── index.html                   # Product landing page
├── .env.example                     # Environment template configuration
├── .gitignore                       # Git exclusion rules
├── .python-version                  # Python runtime specification
├── app.py                           # Core Flask backend server
├── download_models.py               # Hugging Face model downloader script
├── Procfile                         # Render/Heroku process configuration file
├── render.yaml                      # Render Blueprint deployment configuration
└── requirements.txt                 # Application dependencies
```

---

## Future Improvements
- **Real-Time Data Pipelines**: Integrate directly with electronic health record (EHR) systems for automatic metrics importing.
- **Multi-Hospital Analytics**: Compare capacity levels across a region to enable cooperative patient routing.
- **SMS/Email Shortage Alerts**: Automated notifications for hospital leadership when risk scores exceed a set threshold.
- **Deep Learning Model Optimization**: Explore LSTM or transformer-based sequence models to capture long-term seasonality patterns.

---

## Contributors
- **Harish Motamarri** - Developer / Designer

---

## License
This project is licensed under the MIT License - see the LICENSE file for details.
