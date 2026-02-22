# IoMT-Network-Intrusion-Detection-with-Chaotic-Puma-Feature-Selection-and-Deep-Learning
# Network Intrusion Detection with Chaotic Puma Feature Selection and Deep Learning

## Project Overview

This research focuses on building a robust Network Intrusion Detection System (NIDS) using machine learning and deep learning techniques. The primary goal is to classify network traffic into various attack types (e.g., Reconnaissance, Exfiltration, Initial access, Lateral movement) or benign activity. A key component of this project involved implementing a novel metaheuristic feature selection algorithm, 'Chaotic Puma', under aggressive time constraints, to identify the most relevant features for classification. We then trained and evaluated Logistic Regression, Random Forest, and LSTM models on the selected features.

## Dataset

The project utilizes a network traffic dataset, `MedSec-25.csv`, which includes various flow features and a 'Label' column indicating the type of traffic. The dataset comprises 554,534 entries across 84 initial features.

### Label Distribution:

*   Reconnaissance: 401,683
*   Initial access: 102,090
*   Exfiltration: 25,915
*   Lateral movement: 12,498
*   Benign: 12,348

## Methodology

The project followed a comprehensive pipeline:

1.  **Data Loading and Initial Exploration:** Loaded data into a pandas DataFrame, checked dimensions, unique values, missing values, and data types.
2.  **Feature Engineering:**
    *   'Timestamp' was converted to datetime objects, and new features (`Hour`, `DayOfWeek`, `Month`) were extracted.
    *   `Flow ID` was dropped.
    *   `Src IP` and `Dst IP` were one-hot encoded.
    *   The 'Label' column was label encoded into numerical values (0-4).
3.  **Data Preprocessing:**
    *   Numerical features were scaled using `StandardScaler`.
    *   The original 'Timestamp' column was dropped.
4.  **Train-Test Split:** Data was split into training (80%) and testing (20%) sets with `stratify=y` to maintain class distribution.
5.  **Feature Leakage Mitigation:** Highly correlated features (`DayOfWeek`, `Hour`, `Src_IP_192.168.1.108`) were identified and removed, creating `X_reduced`.
6.  **Re-split and Re-scale Reduced Data:** The reduced dataset was re-split and re-scaled.
7.  **Chaotic Puma Feature Selection (on downsampled data):**
    *   A **5% stratified subset** of `X_train_reduced` (`X_train_puma`, `y_train_puma`) was created for feature selection to meet a strict 20-minute time constraint.
    *   A simplified objective function (`evaluate_subset`) was used: a single 80/20 train-validation split with a lightweight `LogisticRegression` model (`max_iter=100`, `solver='liblinear'`).
    *   The 'Chaotic Puma' algorithm was implemented with aggressive parameters (`population_size = 15`, `num_iterations = 7`).
    *   This process identified a subset of **80 optimal features** (from an initial 298 features).
8.  **Model Training and Evaluation (with selected features):** The selected 80 features were applied to the *full* `X_train_reduced` and `X_test_reduced` datasets (`X_train_selected`, `X_test_selected`). Three classification models were then trained and evaluated:
    *   **Logistic Regression**
    *   **Random Forest Classifier**
    *   **Long Short-Term Memory (LSTM) Neural Network**

## Models Implemented and Results

### 1. Logistic Regression Model

*   **Accuracy Score:** **0.9338**
*   **Macro Avg F1-Score:** **0.8692**
*   **Observations:** Performed well for majority classes but struggled with minority classes ('Exfiltration', 'Lateral movement'), showing lower F1-scores and more misclassifications.

### 2. Random Forest Classifier

*   **Accuracy Score:** **0.9893**
*   **Macro Avg F1-Score:** **0.9379**
*   **Observations:** Demonstrated exceptionally strong performance across all classes, including minority ones, with uniformly high F1-scores, indicating a highly robust and accurate model. Significantly outperformed Logistic Regression.

### 3. LSTM Neural Network

*   **Accuracy Score:** **0.9837**
*   **Macro Avg F1-Score:** **0.9171**
*   **Observations:** Achieved excellent performance, particularly for majority classes. Showed improved handling of minority classes compared to Logistic Regression, although 'Exfiltration' and 'Lateral movement' still presented some challenges (e.g., F1-scores of 0.84 and 0.75 respectively). Close in performance to Random Forest.

## Model Comparison Summary

| Metric        | Logistic Regression | Random Forest | LSTM    |
| :------------ | :------------------ | :------------ | :------ |
| **Accuracy**  | 0.9338              | 0.9893        | 0.9837  |
| **Precision** | 0.8857              | 0.9371        | 0.9229  |
| **Recall**    | 0.8717              | 0.9389        | 0.9319  |
| **F1-score**  | 0.8692              | 0.9379        | 0.9171  |

**Visualizations (Radar Chart & Bar Graphs):** Both visualizations clearly show Random Forest as the top performer across all macro-averaged metrics, followed closely by the LSTM model. Logistic Regression lagged behind these two more complex models.

## Chaotic Puma Feature Selection: Compromises & Observations

To adhere to a strict 20-minute time constraint for feature selection, significant compromises were made:

*   **Data Downsampling:** Only a 5% stratified subset of the training data was used for the Chaotic Puma algorithm, drastically reducing computational load.
*   **Simplified Objective Function:** Feature subsets were evaluated using a single 80/20 train-validation split and a lightweight `LogisticRegression` model, prioritizing speed over optimal evaluation.
*   **Aggressive Parameters:** The Chaotic Puma algorithm was configured with minimal iterations (7) and a small population size (15).

Despite these limitations, the algorithm successfully identified a subset of 80 features that yielded a final global best score of `0.9403` on the downsampled data during its internal evaluation. The subsequent strong performance of the Random Forest and LSTM models on the full dataset, even with this constrained feature selection, highlights the effectiveness of the chosen features.

## Conclusion

This project successfully implemented and evaluated an intrusion detection system using a constrained Chaotic Puma feature selection approach. While the feature selection process involved compromises due to time limits, the resulting **Random Forest classifier demonstrated outstanding performance**, achieving an accuracy of **0.9893** and uniformly high F1-scores across all traffic types. The **LSTM model also performed exceptionally well**, with an accuracy of **0.9837**. Both models significantly outperformed Logistic Regression, establishing themselves as highly suitable choices for accurate network intrusion detection. This work underscores that even with constrained metaheuristic feature selection, powerful ensemble and deep learning models can achieve highly effective results for complex classification tasks like network intrusion detection.
