Name:
Manahil Zahra 

-Steel Industry Load Type Prediction System
A complete Machine Learning pipeline built using Scikit-Learn, Principal Component Analysis (PCA), Random Forest, and FastAPI to classify the operational Load Type (Light, Medium, or Maximum Load) of a steel manufacturing plant from real-time electrical readings.
The project covers the full journey from raw data exploration to a deployed, testable prediction API.

- Project Overview
This project uses a real steel industry energy dataset (35,040 rows, 11 columns) to train a classifier that predicts the current Load_Type of the plant.
The complete workflow includes:

Dataset loading & structural inspection
Missing value check
Statistical summary of numerical features
Feature standardization with StandardScaler
Dimensionality reduction with PCA (7 numerical features → 3 components)
Training & benchmarking 4 classification models
Selecting and saving the best-performing model
Building a FastAPI service around the saved pipeline
Exposing the API publicly from Google Colab using localtunnel

📊 Dataset
File used: Week 2 (DataSet).xlsx
Shape: 35,040 rows × 11 columns
Columns:

date (object) — Timestamp of the reading

Usage_kWh (float) — Energy usage in kWh

Lagging_Current_Reactive.Power_kVarh (float) — Lagging reactive power

Leading_Current_Reactive_Power_kVarh (float) — Leading reactive power

CO2(tCO2) (float) — CO2 emissions

Lagging_Current_Power_Factor (float) — Lagging power factor

Leading_Current_Power_Factor (float) — Leading power factor

NSM (int) — Number of seconds since midnight

WeekStatus (object) — Weekday / Weekend

Day_of_week (object) — Day name

Load_Type (object) — Target: Light / Medium / Maximum Load

Only the 7 numerical columns above are used as input features for PCA; date, WeekStatus, and Day_of_week are excluded from the model.

🧠 Machine Learning Workflow
Raw Dataset (35,040 rows × 11 columns)
→ Select 7 Numerical Features
→ StandardScaler (mean = 0, variance = 1)
→ PCA — 7 Components → Explained Variance Analysis
→ PCA — Reduced to 3 Components (~93% variance retained)
→ Train/Test Split (80/20, stratified on Load_Type)
→ Benchmark 4 Classifiers
→ Champion: Random Forest
→ Save model.pkl + scaler.pkl + pca.pkl
→ FastAPI /predict endpoint
PCA Explained Variance (all 7 components):

PC1: 48.54%
PC2: 36.90%
PC3: 8.21%
PC4: 4.33%
PC5: 1.17%
PC6: 0.69%
PC7: 0.16%

The first 3 components were kept, retaining roughly 93.65% of the total variance while reducing the feature space from 7 dimensions to 3.
Train/Test Split:

Training set: 28,032 samples
Testing set: 7,008 samples
Split ratio: 80/20, stratified on Load_Type

📈 Model Comparison Results
Four classifiers were trained on the 3 PCA components and compared by accuracy:

Logistic Regression: 70.28%
Ridge Classifier: 68.07%
Decision Tree: 84.10%
Random Forest (Champion): 86.33%

Random Forest was selected as the final model and deployed in the API.

🖥️ API Endpoints

GET / → Health check / welcome message
POST /predict → Returns predicted Load Type from 7 raw readings

Request body (/predict):
{
"Usage_kWh": 3.5,
"Lagging_Current_Reactive_Power_kVarh": 2.1,
"Leading_Current_Reactive_Power_kVarh": 0.0,
"CO2_tCO2": 0.02,
"Lagging_Current_Power_Factor": 85.5,
"Leading_Current_Power_Factor": 100.0,
"NSM": 40000
}
Response:
{
"prediction": "Light_Load",
"features_pca": {
"PC1": 1.23,
"PC2": -0.45,
"PC3": 0.67
}
}
Internally, the API scales the raw input with the saved scaler.pkl, transforms it with pca_transformer.pkl, and passes the resulting 3 components to best_random_forest_model.pkl for the final prediction.

Install dependencies
pip install -r requirements.txt

- Retraining the Model
To rebuild everything from scratch, simply re-run the notebook end-to-end:
jupyter nbconvert --to notebook --execute Steel_Industry_Load_Prediction.ipynb
This regenerates best_random_forest_model.pkl, scaler.pkl, and pca_transformer.pkl with the latest data.

- Technology Stack
Backend: FastAPI, Uvicorn
Machine Learning: Scikit-Learn (StandardScaler, PCA, Logistic Regression, Ridge Classifier, Decision Tree, Random Forest), Joblib
Data Processing: Pandas, NumPy
Visualization: Matplotlib
Deployment (Colab): nest_asyncio, pyngrok / localtunnel

   -Project Objective
To accurately classify the operational load type of a steel manufacturing facility while reducing feature dimensionality through PCA, and to deploy the resulting model as a usable, real-time prediction API.
