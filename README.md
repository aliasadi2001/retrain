## Retraining Time

To keep your anomaly detection model up-to-date as traffic patterns evolve, you can automatically trigger retraining based on concept-drift signals. Below are two common approaches:

### 1. Statistical Parameter Monitoring  
1. **Baseline statistics**  
   - Compute key distribution metrics (e.g., mean, variance, percentiles) on your original training dataset.  
2. **Current window statistics**  
   - On each time slot (e.g. every hour or day), compute the same metrics for the incoming data batch.  
3. **Drift thresholding**  
   - Compare baseline vs. current metrics using measures like Population Stability Index (PSI), KL-divergence or Kolmogorov–Smirnov distance.  
   - If any metric exceeds a predefined threshold (e.g. PSI > 0.2), mark drift detected and schedule retraining.

### 2. Window-Based Drift Detection  
1. **Sliding windows**  
   - Maintain two dynamic windows: a reference window of past data and a recent window of incoming data.  
2. **Adaptive algorithms**  
   - Use methods like ADWIN (Adaptive Windowing) or DDM/EDDM (Drift Detection Method) to automatically adjust window sizes and detect statistically significant changes.  
3. **Retraining trigger**  
   - When the drift detector flags a change (e.g. ADWIN signals a drop in average SINR), enqueue a retraining job on the combined dataset from both windows.

---

> **Next Steps / TODO**  
> - Define exact window lengths and overlap strategies.  
> - Calibrate thresholds (PSI, ADWIN delta) on historical data to balance sensitivity vs. stability.  
> - Implement an automated pipeline (e.g., Airflow/Kubeflow) that:  
>   1. Monitors drift metrics in real time  
>   2. Triggers retraining only when persistent drift is confirmed  
>   3. Validates and deploys the updated model with canary rollout  



# Model Retraining with Unbalanced Data

In real-world applications, data distributions often become imbalanced over time. When retraining models on such data, it’s important to handle this imbalance to avoid biased or inaccurate predictions.

## Solutions

### 1. Oversampling

Oversampling increases the number of minority class samples to balance the dataset.

- **SMOTE (Synthetic Minority Over-sampling Technique)**: Generates synthetic examples by interpolating between nearby minority class samples.
- **ADASYN (Adaptive Synthetic Sampling)**: Similar to SMOTE, but focuses on generating more synthetic data for harder-to-learn samples near the decision boundary.

### 2. Undersampling

Undersampling reduces the number of majority class samples.

- **Random Undersampling**: Randomly removes samples from the majority class.
- **NearMiss**: Selects majority samples that are closest to minority class examples to maintain decision boundary clarity.

## Why Data Augmentation is Not Recommended

Data augmentation is effective in domains like images or text, where transformations (e.g., rotation, synonym replacement) preserve the semantics. In tabular data:

- There are no universal augmentation rules.
- Artificial patterns can introduce noise.
- Augmented data might lead to overfitting on unrealistic or synthetic patterns.

Instead, focus on using oversampling and undersampling methods specifically designed for tabular anomaly detection.

# retrain
