# Credit Risk Classification API

An ML-powered credit risk assessment system that estimates the
probability of loan default from an applicant's financial and
credit-profile information. It combines a machine-learning model, a
FastAPI backend, and a responsive web interface to return a **Low Risk**
or **High Risk** assessment.

> **Disclaimer:** This project is for educational and demonstration
> purposes only. Predictions are model estimates, not financial advice
> or a lending decision.

## Features

-   Estimates loan-default probability using a trained classification
    model.
-   Uses a saved decision threshold to classify applications as Low Risk
    or High Risk.
-   Provides a FastAPI `/predict` endpoint.
-   Validates incoming applicant data with Pydantic.
-   Serves an interactive HTML, CSS, and JavaScript frontend through
    FastAPI.
-   Loads the trained model and threshold from Joblib files when the API
    starts.

## Tech Stack

  Component             Technology
  --------------------- -----------------------
  Backend API           FastAPI
  Data validation       Pydantic
  Data handling         Pandas
  Machine learning      Scikit-learn, XGBoost
  Model serialization   Joblib
  Frontend              HTML, CSS, JavaScript
  ASGI server           Uvicorn
  Model development     Jupyter Notebook

## Project Structure

``` text
Credit-Risk-Classification-API/
│
├── static/
│   ├── index.html
│   ├── style.css
│   └── script.js
│
├── Credit_Risk.ipynb
├── credit_risk_dataset.csv
├── credit_risk_model.pkl
├── best_threshold.pkl
├── main.py
├── requirements.txt
├── runtime.txt
├── render.yaml
└── README.md
```

## Input Features

The prediction endpoint accepts these applicant and loan attributes:

  Field                          Description
  ------------------------------ ---------------------------------------
  `person_age`                   Applicant's age
  `person_income`                Applicant's annual income
  `person_home_ownership`        Home ownership status
  `person_emp_length`            Length of employment
  `loan_intent`                  Purpose of the loan
  `loan_grade`                   Loan grade
  `loan_amnt`                    Requested loan amount
  `loan_int_rate`                Loan interest rate
  `loan_percent_income`          Loan amount as a proportion of income
  `cb_person_default_on_file`    Historical default indicator
  `cb_person_cred_hist_length`   Length of credit history in years

The web interface calculates `loan_percent_income` from loan amount and
annual income.

## How It Works

1.  The applicant enters details in the web interface.
2.  JavaScript sends the details as JSON to the FastAPI `/predict`
    endpoint.
3.  Pydantic validates the request using the `LoanApplication` schema.
4.  The API converts the input into a Pandas DataFrame.
5.  The trained model estimates default probability using
    `predict_proba()`.
6.  The probability is compared with the threshold loaded from
    `best_threshold.pkl`.
7.  The API returns the probability, threshold, binary prediction, and
    risk label.
8.  The frontend displays the assessment.

### Example prediction response

``` json
{
  "default_probability": 0.23,
  "default_prediction": 0,
  "threshold": 0.35,
  "Result": "Low Risk"
}
```

The values above are illustrative only, not measured model results.

-   `default_probability`: Estimated probability of default, between 0
    and 1.
-   `default_prediction`: `1` indicates High Risk; `0` indicates Low
    Risk.
-   `threshold`: Decision threshold loaded from `best_threshold.pkl`.
-   `Result`: Human-readable risk classification.

## Run Locally

### 1. Clone the repository

``` bash
git clone https://github.com/Jatinkumar-15/Credit-Risk-Classification-API.git
cd Credit-Risk-Classification-API
```

### 2. Create and activate a virtual environment

**Windows PowerShell:**

``` powershell
python -m venv ml_env
.\ml_env\Scripts\Activate.ps1
```

If PowerShell blocks activation for the current terminal session:

``` powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\ml_env\Scripts\Activate.ps1
```

**macOS / Linux:**

``` bash
python3 -m venv ml_env
source ml_env/bin/activate
```

### 3. Install dependencies

``` bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Start the API

Run this command from the project root (the folder containing `main.py`
and the model files):

``` bash
uvicorn main:app --reload
```

### 5. Open the application

-   Web interface: `http://127.0.0.1:8000`
-   Interactive API documentation: `http://127.0.0.1:8000/docs`
-   OpenAPI schema: `http://127.0.0.1:8000/openapi.json`

Ensure `credit_risk_model.pkl`, `best_threshold.pkl`, and the `static/`
directory are present before starting the application.

## API Endpoint

### `POST /predict`

Accepts applicant information as JSON and returns a credit risk
assessment.

Example request:

``` json
{
  "person_age": 30,
  "person_income": 600000,
  "person_home_ownership": "RENT",
  "person_emp_length": 5.0,
  "loan_intent": "PERSONAL",
  "loan_grade": "A",
  "loan_amnt": 100000,
  "loan_int_rate": 11.5,
  "loan_percent_income": 0.17,
  "cb_person_default_on_file": "N",
  "cb_person_cred_hist_length": 6
}
```

Use category values and formatting consistent with the model's training
data. This example is illustrative and does not guarantee a particular
prediction.

## Dependencies

The versions pinned in `requirements.txt` are:

``` text
fastapi==0.162.7
uvicorn==0.54.0
pydantic==2.14.3
pandas==2.3.2
scikit-learn==1.9.0
xgboost==3.4.1
joblib==1.5.2
```

The configured Python runtime is specified in `runtime.txt`.

## Deployment

The repository includes `render.yaml` for Render deployment. The
deployed service needs the dependencies, `main.py`, the `static/`
directory, and both model artifact files.

For platforms that provide a `PORT` environment variable, use:

``` bash
uvicorn main:app --host 0.0.0.0 --port $PORT
```

Use a Python version compatible with the pinned packages and the
environment used to create the model artifacts.

## Important Notes

-   The model and threshold files must match the feature columns,
    preprocessing, and class-label convention used during training.
-   Keep the training notebook and API inference code consistent when
    changing feature engineering or categorical values.
-   A risk estimate is not an approval or rejection decision.
-   Do not commit secrets, credentials, or private applicant
    information.

## Future Improvements

-   Add automated tests for request validation and prediction responses.
-   Document model evaluation results and add explainability tools.
-   Improve input constraints and user-facing validation messages.
-   Monitor prediction distributions and model drift.
-   Track model versions and document dataset provenance.

## Author

**Jatin Kumar**

GitHub: [@Jatinkumar-15](https://github.com/Jatinkumar-15)
