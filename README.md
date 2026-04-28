# IoMT Cyber Security Threat Detection using Chaotic Puma Wrapper Based Feature Selection

## Overview
This notebook demonstrates the development and comparison of various machine learning models for an Intrusion Detection System (IDS). The goal is to classify network traffic into different attack types or benign activity, enabling the detection of cyber threats.

## Dataset
The dataset used in this project is `MedSec-25.csv`, which contains network flow features. Key characteristics of the dataset include:
*   **Total rows:** 554,534
*   **Total columns:** 84 (initially)
*   **Target Variable:** `Label` (Categorical: Reconnaissance, Initial access, Exfiltration, Lateral movement, Benign)
*   **Class Distribution:** Highly imbalanced, with 'Reconnaissance' being the dominant class.

## Methodology
The project followed a structured machine learning pipeline:

1.  **Data Loading and Initial Exploration:**
    *   Loaded the `MedSec-25.csv` file into a Pandas DataFrame.
    *   Checked for missing values (none found).
    *   Examined data types and descriptive statistics.

2.  **Feature Engineering:**
    *   Extracted `Hour`, `DayOfWeek`, and `Month` from the `Timestamp` column.
    *   One-hot encoded categorical IP address columns (`Src IP`, `Dst IP`).
    *   Dropped the original `Timestamp` and `Flow ID` columns.

3.  **Data Preprocessing:**
    *   **Label Encoding:** The `Label` (target) column was encoded into numerical values.
    *   **Feature Scaling:** Numerical features were scaled using `StandardScaler`.
    *   **Feature Selection (Chaotic Puma Algorithm):** To optimize model training and performance, a custom Chaotic Puma Algorithm was implemented on a downsampled subset of the training data. This heuristic approach identified a reduced set of highly relevant features.
    *   **Data Splitting:** The dataset was split into training and testing sets (80/20 ratio) with stratification to maintain class distribution.

4.  **Model Training and Evaluation:**
    Three classification models were trained and comprehensively evaluated:
    *   **Logistic Regression**
    *   **Random Forest Classifier**
    *   **Long Short-Term Memory (LSTM) Neural Network**

    Model performance was assessed using:
    *   Accuracy, Precision, Recall, F1-score (macro-averaged for class imbalance).
    *   Confusion Matrices and F1-score plots for per-class performance visualization.
    *   **McNemar's Test:** Statistical comparison of model pairs (Random Forest vs. LSTM, Random Forest vs. Logistic Regression) to determine significant performance differences.
    *   **AUC-ROC Score:** Macro-average AUC-ROC to evaluate classifier's ability to distinguish between classes.
    *   **Cohen's Kappa Score:** Measures inter-rater agreement, accounting for chance agreement.
    *   **5-Fold Stratified Cross-Validation:** Performed on the best-performing model (Random Forest) to ensure robustness and generalization ability.

## Key Findings

### Overall Model Performance Comparison (Macro-Average Metrics)

| Model               | Accuracy | Precision | Recall   | F1-score |
| :------------------ | :------- | :-------- | :------- | :------- |
| Logistic Regression | 0.944819 | 0.902280  | 0.898126 | 0.889105 |
| Random Forest       | 0.991362 | 0.944025  | 0.948358 | 0.945995 |
| LSTM                | 0.980822 | 0.920155  | 0.943102 | 0.920688 |


### Detailed Per-Class Performance Comparison

| Model               | Class            | Precision | Recall | F1-Score | Support |
| :------------------ | :--------------- | :-------- | :----- | :------- | :------ |
| Logistic Regression | Benign           | 1.0000    | 1.0000 | 1.0000   | 2469    |
| Logistic Regression | Exfiltration     | 0.9879    | 0.7220 | 0.8342   | 5183    |
| Logistic Regression | Initial access   | 0.9332    | 0.8334 | 0.8805   | 20418   |
| Logistic Regression | Lateral movement | 0.6324    | 0.9496 | 0.7592   | 2500    |
| Logistic Regression | Reconnaissance   | 0.9579    | 0.9857 | 0.9716   | 80337   |
| Random Forest       | Benign           | 1.0000    | 0.9996 | 0.9998   | 2469    |
| Random Forest       | Exfiltration     | 0.9252    | 0.8968 | 0.9107   | 5183    |
| Random Forest       | Initial access   | 0.9988    | 0.9993 | 0.9990   | 20418   |
| Random Forest       | Lateral movement | 0.7964    | 0.8464 | 0.8206   | 2500    |
| Random Forest       | Reconnaissance   | 0.9998    | 0.9997 | 0.9998   | 80337   |
| LSTM                | Benign           | 0.9996    | 0.9992 | 0.9994   | 2469    |
| LSTM                | Exfiltration     | 0.9907    | 0.7393 | 0.8468   | 5183    |
| LSTM                | Initial access   | 0.9672    | 0.9987 | 0.9827   | 20418   |
| LSTM                | Lateral movement | 0.6437    | 0.9872 | 0.7793   | 2500    |
| LSTM                | Reconnaissance   | 0.9996    | 0.9911 | 0.9953   | 80337   |

### Statistical Test Results
*   **McNemar's Test (Random Forest vs. LSTM):** Statistically significant difference (p-value < 0.0001). Random Forest was found to be significantly better than LSTM.
*   **McNemar's Test (Random Forest vs. Logistic Regression):** Statistically significant difference (p-value < 0.0001). Random Forest was found to be significantly better than Logistic Regression.
*   **AUC-ROC:** Random Forest (0.9992) slightly outperformed LSTM (0.9977) (macro-average).
*   **Cohen's Kappa:**
    *   Random Forest vs. True Labels: 0.9803 (Almost perfect agreement)
    *   LSTM vs. True Labels: 0.9566 (Almost perfect agreement)
    *   Random Forest vs. LSTM predictions: 0.9598 (Almost perfect agreement)

### Cross-Validation for Random Forest
*   **Mean 5-fold cross-validation accuracy:** 0.9911
*   **Standard deviation:** 0.0001

## Conclusion
The **Random Forest Classifier** emerged as the most robust and highest-performing model for this intrusion detection task. It achieved exceptional overall accuracy and balanced performance across all attack classes, supported by strong statistical evidence from McNemar's tests, high AUC-ROC scores, and almost perfect Cohen's Kappa agreement with true labels. The very low standard deviation in cross-validation accuracy further confirms its excellent generalization capabilities and stability.

While the LSTM model also showed strong performance, particularly for a deep learning approach with single time-step inputs, Random Forest consistently demonstrated a slight edge in reliability and overall effectiveness.
