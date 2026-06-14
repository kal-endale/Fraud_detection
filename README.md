# Fraud_detection

This project focuses on building and evaluating machine learning models for fraud detection using two distinct datasets: Fraud_Data.csv and creditcard.csv.

Table of Contents
Project Overview
Datasets
Data Cleaning and Preprocessing
Exploratory Data Analysis (EDA)
Feature Engineering
Model Training and Evaluation
Model Explainability (SHAP Analysis)
Conclusion and Recommendations
1. Project Overview
The primary goal of this project is to develop robust fraud detection models capable of identifying fraudulent transactions while handling highly imbalanced datasets. The project covers the entire machine learning pipeline, from data ingestion and cleaning to advanced model explainability techniques.

2. Datasets
Two datasets are utilized in this project:

2.1. Fraud_Data.csv
This dataset contains information about user signups and subsequent purchases, along with device and IP address details. It includes features like user_id, signup_time, purchase_time, purchase_value, device_id, source, browser, sex, age, ip_address, and a class label indicating fraud (1) or non-fraud (0).

2.2. IpAddress_to_Country.csv
This supplementary dataset maps IP address ranges to countries, used to enrich the Fraud_Data.csv with geographical information.

2.3. creditcard.csv
This dataset contains credit card transaction data, heavily anonymized due to confidentiality issues. It includes Time, Amount, and 28 principal components (V1 through V28) obtained with PCA. The Class column indicates fraudulent transactions (1) or legitimate transactions (0).

3. Data Cleaning and Preprocessing
Missing Values: Checked for and confirmed no missing values in either fraud_data or ip_country_data.
Duplicates: No duplicate rows were found in fraud_data. The creditcard.csv contained duplicates which were handled.
Data Types: Converted signup_time and purchase_time to datetime objects in fraud_data for time-based feature engineering. ip_address columns were converted to integer types for efficient lookup.
4. Exploratory Data Analysis (EDA)
Fraud_Data.csv
Class Imbalance: Significant imbalance was observed (approx. 90.6% non-fraud, 9.4% fraud).
Distribution Analysis: Visualized distributions of purchase_value, age, source, browser, and sex.
Geolocation Analysis: Integrated ip_country_data to map IP addresses to countries and analyzed fraud patterns by country.
creditcard.csv
Class Imbalance: Extreme imbalance (approx. 99.8% non-fraud, 0.2% fraud).
Feature Distributions: Visualized distributions of Time and Amount for both fraudulent and non-fraudulent transactions.
5. Feature Engineering
For fraud_data:

Time-based Features: purchase_hour, purchase_day_of_week were extracted from purchase_time.
time_since_signup: Calculated as the duration between purchase_time and signup_time.
device_transaction_count: Cumulative count of transactions per device_id, serving as a proxy for device activity.
Categorical Encoding: One-Hot Encoding was applied to source, browser, sex, and country.
Numerical Scaling: StandardScaler was used for numerical features (purchase_value, age, ip_address, time_since_signup, purchase_hour, purchase_day_of_week, device_transaction_count).
6. Model Training and Evaluation
Data Split and Resampling
Data was split into training (70%) and testing (30%) sets using stratify=y to maintain class distribution.
SMOTE (Synthetic Minority Oversampling Technique) was applied to the training data to address class imbalance, creating X_train_resampled and y_train_resampled.
Models Implemented
Logistic Regression: A baseline model trained on the resampled data.

F1-Score (Test): 0.5975
AUC-PR (Test): 0.6042
ROC AUC (Test): 0.7968
Random Forest Classifier: An ensemble model, tuned using GridSearchCV with StratifiedKFold.

Best Parameters: max_depth=10, n_estimators=50
F1-Score (Test): 0.6368
AUC-PR (Test): 0.6774
ROC AUC (Test): 0.8090
Model Selection
The Random Forest model was selected as the best performer due to its superior F1-Score and AUC-PR, which are critical metrics for imbalanced fraud detection datasets. While Logistic Regression is more interpretable, the performance gain from Random Forest outweighs the slight loss in direct interpretability for this critical task.

7. Model Explainability (SHAP Analysis)
SHAP (SHapley Additive exPlanations) analysis was conducted on the best Random Forest model to understand feature contributions to predictions.

Key Findings:
Feature Importance: Both built-in feature importance and SHAP summary plots consistently identified device_transaction_count and time_since_signup as the most influential features for fraud prediction.
Top 5 Drivers of Fraud (SHAP):
device_transaction_count
time_since_signup
country_United States
purchase_day_of_week
browser_Chrome
Surprising Findings: purchase_value and age had relatively low importance compared to behavioral features, suggesting that how transactions occur and user behavior patterns are more indicative of fraud than traditional demographic or transaction value data.
SHAP Plots Generated:
SHAP Summary Plot (Global Feature Importance for Fraud Class)
SHAP Force Plots for individual instances:
True Positive
False Positive
False Negative
8. Conclusion and Recommendations
Based on the model's performance and explainability, the Random Forest model effectively identifies fraudulent transactions. The SHAP analysis provides actionable insights:

Business Recommendations:
Implement Real-time Device-Based Transaction Monitoring: Focus on device_transaction_count. Develop alerts for unusually high transaction volumes from single devices to flag suspicious activity.
Enhance Onboarding and Purchase Flow Security for Delayed Transactions: The time_since_signup feature suggests increased fraud risk for accounts with long periods between signup and purchase. Introduce additional verification for such transactions.
Geospatial and Browser-Specific Risk Assessment: Integrate country and browser information into a comprehensive risk scoring system. Transactions originating from specific high-risk countries or browsers, especially when combined with other fraud indicators, should trigger higher scrutiny.
