# Run Log - Quantum-Based Anomaly Detection in Network Traffic Using Qiskit’s Variational Quantum Classifier

**Student:** Mir Hasan Mohammad Ilham  
**Project:** Quantum-Based Network Traffic Anomaly Detection using VQC  
**Repository:** https://github.com/Mir-Ilham/SQA-2026-Quantum-Based-Network-Anomaly-Detection-VQC  
**Date started:** 28 March 2026  
**Environment:** Local (Python 3.11.9) + qiskit 2.3.0, qiskit-machine-learning 0.9.0

## Run 1 - Baseline (Working Copy)

**Date:** 28 Mar 2026  
**Changes made:** View [change_log.md](./change_log.md) file for changes made in the original notebook

**Baseline Parameters**

```
  {
      "random_seed": 42,
      "n_samples": 1000,
      "anomaly_ratio": 0.5,
      "test_size": 0.3,
      "maxiter": 100  # (40 for VQC-3)
  }
```

### Exploratory Data Analysis:

#### 1. Pair plot of data feature distribution

<img src="results/run1/Data-Feature-distribution.png" alt="Data Feature Distribution" width="50%" height="50%">

#### 2. Principal Component Analysis

<img src="results/run1/PCA.png" alt="PCA" width="40%" height="40%">

**Observations:** The pair plot along with the PCA visualization shows very clear separability between normal (blue) and anomalous (orange) traffic, indicating unique behavioral patterns in the dataset. Normal traffic clusters tightly around lower packet rates (~100 packets/sec), lower byte volumes, and very low packet drop rates (~1%), indicating low variance and stable behavior. In contrast, anomalous traffic exhibits significantly higher packet rates and byte volumes with much greater spread, along with elevated and widely dispersed packet drop rates (~0.1). The protocol feature also cleanly separates the classes, with normal traffic concentrated at protocol 1 and anomalies distributed across protocols 2 and 3.

#### 3. Feature Correlation Heatmap

<img src="results/run1/Correlation-heatmap.png" alt="Correlation Heatmap" width="40%" height="40%">

**Observations:** The heatmap shows strong positive correlations (~0.86–0.88) between packet rate, byte rate, and protocol, indicating that higher traffic intensity is consistently linked with protocol shifts. Packet drop rate is also highly correlated (~0.81–0.87) with these features, suggesting congestion-induced packet loss under heavy load.

**Interpretation of results:** From a networking perspective, these patterns suggest that normal traffic corresponds to stable TCP-based communication with consistent throughput and minimal loss, while anomalies reflect bursty, high-intensity traffic often associated with flooding or abnormal transfers. The increased packet drop rates indicate congestion or instability, commonly seen during attacks or misconfigurations. The protocol shift toward UDP/ICMP further reinforces this, as such protocols are frequently used in scanning or DDoS scenarios due to their connectionless nature.

### Model Performance Summary

<img src="results/run1/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, maxiter=100, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run1/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run1/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Stable convergence with decent accuracy (~0.72), but high false negatives (missed anomalies)

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, maxiter=100, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run1/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run1/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Weakest performance (0.573), struggles heavily with anomaly detection

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, maxiter=40, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run1/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run1/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Significant improvement (~0.85), reduced false negatives noticeably.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, maxiter=100, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run1/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run1/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Best performance (~0.89), strong convergence and balanced predictions.

### Classical Model Benchmarks: SVM & RandomForest

#### SVM

**Key parameters:** `kernel='rbf'`, `C=1.0`, `gamma='scale'`

<img src="results/run1/SVM-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, SVM" width="40%" height="40%">

#### RandomForest

**Key parameters:** `n_estimators=100`, `random_state=42`

<img src="results/run1/Random-Forest-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, RandomForest" width="40%" height="40%">

**Observations:** Both `RandomForest` and `SVM` (RBF kernel) achieve perfect performance (100% across all metrics), indicating that the dataset is highly separable in feature space. This suggests that classical models can easily learn the decision boundary due to strong feature correlations and clear distribution shifts, highlighting that the current data setup is relatively simple.

### VQC Model-4 Misclassification Analysis

#### Misclassification analysis via PCA

<img src="results/run1/VQC-Model-4-Misclassification-analysis-PCA.png" alt="Misclassification analysis PCA" width="40%" height="40%">

#### Misclassifications by true label

<img src="results/run1/VQC-Model-4-Misclassifications-by-true-label.png" alt="Misclassifications by true label" width="40%" height="40%">

**Observations:** The PCA plot shows that most misclassifications occur within the anomalous cluster, particularly in regions where data points are more dispersed. Normal samples remain tightly clustered and are almost perfectly classified, reflecting strong separability for that class. The misclassification count further confirms this imbalance, with significantly more errors in anomalies than normal traffic.

### Performance Comparison

<img src="results/run1/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** The comparison shows a performance hierarchy where VQC-2 performs the worst, followed by VQC-1, while VQC-3 and especially VQC-4 (deep circuit) show strong improvements, indicating that increased circuit depth and better optimizers significantly enhance learning capacity. However, both classical models (SVM and Random Forest) achieve perfect scores across all metrics, confirming that the dataset is linearly/nonlinearly separable enough for classical methods to fully capture. Overall, this highlights that while VQCs can approximate the decision boundary with increasing expressivity, they still lag behind classical models in efficiency and optimization on this dataset.

---

## Run 2 - Random seed set to 7

**Parameters:** View [Parameters for this run](./experiments/parameters_run2.json)

```
  {
      "run": 2,
      "description": "Random seed set to 7",
      "random_seed": 7,
      "n_samples": 1000,
      "anomaly_ratio": 0.5,
      "test_size": 0.3,
      "maxiter": 100
  }
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run2.json)

### Model Performance Summary

<img src="results/run2/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run2/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run2/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Varying the random seed has a negligible effect on model training. Model converges approximately to the same point as the baseline. Accuracy (~0.72) remains consistent, along with high false negative cases.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run2/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run2/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Lowest performance among the four models, similar to the baseline run. Almost completely misclassifies the anomalous samples. The F1-Score (0.35) indicates poor generalization of decision boundary to properly distinguish the two classes.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run2/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run2/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Loss curve simila to baseline. Slight accuracy improvement (~0.88) and reduced false negatives compared to baseline run. However, it should be noted that the baseline run had `40` max iterations as it was in the original notebook. Increasing to `100` iterations, resulted in very slight improvements.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run2/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run2/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Higher loss plateau. Significant decrease in performance from an accuracy of ~0.89 to ~0.78. False predictions (negative and positive) increased compared to baseline run. The model shows high sensitivity to random initialization, where different seeds lead to convergence in different local minima, resulting in noticeable performance variation.

### Classical Model Benchmarks: SVM & RandomForest

#### SVM

**Key parameters:** `kernel='rbf'`, `C=1.0`, `gamma='scale'`

<img src="results/run2/SVM-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, SVM" width="40%" height="40%">

#### RandomForest

**Key parameters:** `n_estimators=100`, `random_state=42`

<img src="results/run2/Random-Forest-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, RandomForest" width="40%" height="40%">

**Observations:** Both `RandomForest` and `SVM` (RBF kernel) achieve perfect performance (100% across all metrics), similar to the baseline run.

### VQC Model-4 Misclassification Analysis

#### Misclassification analysis via PCA

<img src="results/run2/VQC-Model-4-Misclassification-analysis-PCA.png" alt="Misclassification analysis PCA" width="40%" height="40%">

#### Misclassifications by true label

<img src="results/run2/VQC-Model-4-Misclassifications-by-true-label.png" alt="Misclassifications by true label" width="40%" height="40%">

**Observations:** Previous observation on the misclassifications still hold. However, in this run an increase in the amount of misclassifications is observed.

### Performance Comparison

<img src="results/run2/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** The comparison shows a performance hierarchy where VQC-2 performs the worst, followed by VQC-1. In contrast to the baseline run, VQC-4 significantly reduced in performance and the best performing model for this run is VQC-3.

---

## Run 3 - Random seed set to 100

**Parameters:** View [Parameters for this run](./experiments/parameters_run3.json)

```
{
    "run": 3,
    "description": "Random seed set to 100",
    "random_seed": 100,
    "n_samples": 1000,
    "anomaly_ratio": 0.5,
    "test_size": 0.3,
    "maxiter": 100
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run3.json)

### Model Performance Summary

<img src="results/run3/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run3/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run3/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Varying the random seed has a negligible effect on model training. Accuracy (~0.72) remains consistent with the baseline run, along with high false negative cases.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run3/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run3/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Increase in F1-Score (~0.42). High percentage of false negatives maintained.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run3/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run3/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Similar loss plateau as baseline. Accuracy improvement (~0.91) and reduced false negatives compared to baseline run.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run3/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run3/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Higher loss plateau. Significant decrease in performance from an accuracy of ~0.89 to ~0.76. False predictions (negative and positive) increased compared to baseline run.

### Performance Comparison

<img src="results/run3/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** Varying the random seed leads to convergence in different local minima, resulting in noticeable performance variation in VQC-3 and VQC-4. This indicates instability in the optimization process of VQC Models due to the non-convex nature of loss functions for these models.

---

## Run 4 - Number of samples set to 500

**Parameters:** View [Parameters for this run](./experiments/parameters_run4.json)

```
{
    "run": 4,
    "description": "Number of samples set to 500",
    "random_seed": 42,
    "n_samples": 500,
    "anomaly_ratio": 0.5,
    "test_size": 0.3,
    "maxiter": 100
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run4.json)

### Model Performance Summary

<img src="results/run4/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### Exploratory Data Analysis:

#### 1. Pair plot of data feature distribution

<img src="results/run4/Data-Feature-distribution.png" alt="Data Feature Distribution" width="50%" height="50%">

#### 2. Principal Component Analysis

<img src="results/run4/PCA.png" alt="PCA" width="40%" height="40%">

**Observations:** Reducing the number of training samples resulted in notably less dense clustering of the data points. Anomalous samples are more widely scattered than normal samples.

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run4/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run4/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** There is a marginal reduction in loss. Training time is reduced by approximately 50% compared to the baseline. Although performance metrics improved, these gains are primarily attributable to the smaller test set rather than to improved modeling of class separation.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run4/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run4/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Reduction in training time and loss. High percentage of false negatives maintained. However, there is improvement observed across all performance metrics.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run4/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run4/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Reduction in training time for `100` optimizer iterations. Lower loss plateau as observed from the loss curve. Similar to the case of VQC-1, there is an increase throughout all evaluation metrics due to a smaller test set.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run4/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run4/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Training time reduced. Optimizer convergence did not follow a smooth descent and plateaued higher. Performance is similar to baseline due to the same `random_seed` initialization.

### Performance Comparison

<img src="results/run4/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** The performance hierarchy is clearly evident. VQC-2 performed the worst, followed by VQC-1, while VQC-4 and VQC-3 achieved the best results. The observed improvements in VQC-1 and VQC-3 are primarily attributable to the smaller test set rather than to enhanced model performance.

---

## Run 5 - Number of samples set to 2000

**Parameters:** View [Parameters for this run](./experiments/parameters_run5.json)

```
{
    "run": 5,
    "description": "Number of samples set to 2000",
    "random_seed": 42,
    "n_samples": 2000,
    "anomaly_ratio": 0.5,
    "test_size": 0.3,
    "maxiter": 100
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run5.json)

### Model Performance Summary

<img src="results/run5/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### Exploratory Data Analysis:

#### 1. Pair plot of data feature distribution

<img src="results/run5/Data-Feature-distribution.png" alt="Data Feature Distribution" width="50%" height="50%">

#### 2. Principal Component Analysis

<img src="results/run5/PCA.png" alt="PCA" width="40%" height="40%">

**Observations:** Increasing the number of training samples resulted in notably denser clustering of the data points. Anomalous samples are more widely scattered than normal samples.

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run5/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run5/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Training time is increased by more than a factor of `2` compared to the baseline. Marginal improvements in the performance metrics.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run5/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run5/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Training time increased by about a factor of `2`. High percentage of false negatives maintained. Model struggles heavily in detecting anomalous samples.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run5/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run5/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Training time increased by about a factor of `2` for `100` optimizer iterations. Significant decrease in performance compared to all previous runs. A higher percentage of false negatives observed.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run5/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run5/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Higher loss plateau. Training time increased by about a factor of `2`. Optimizer convergence follows a stable descent. Marginal decrease in performance compared to baseline.

### Performance Comparison

<img src="results/run5/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** The performance hierarchy is clearly evident. VQC-2 performed the worst, followed by VQC-1, while VQC-4 and VQC-3 achieved the best results.

---

## Run 6 - Anomaly ratio set to 0.3

**Parameters:** View [Parameters for this run](./experiments/parameters_run6.json)

```
{
    "run": 6,
    "description": "Anomaly ratio set to 0.3",
    "random_seed": 42,
    "n_samples": 1000,
    "anomaly_ratio": 0.3,
    "test_size": 0.3,
    "maxiter": 100
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run6.json)

### Model Performance Summary

<img src="results/run6/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### Exploratory Data Analysis:

#### 1. Pair plot of data feature distribution

<img src="results/run6/Data-Feature-distribution.png" alt="Data Feature Distribution" width="50%" height="50%">

#### 2. Principal Component Analysis

<img src="results/run6/PCA.png" alt="PCA" width="40%" height="40%">

**Observations:** A greater proportion of samples are now generated as non-anomalous. The non-anomalous cluster increased in size, while the anomalous cluster became less densely populated.

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run6/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run6/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Although the overall accuracy increased compared to the baseline, there is a significant decrease in precision and recall that is reflected in the F1-score.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run6/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run6/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** High percentage of false negatives maintained. All the test-set anomalous samples are misclassified. On the other hand, all normal instances are correctly classified, indicating model bias. The likely cause could be that the model has learnt a degenerate decision boundary.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run6/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run6/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Smooth descent of loss curve and a better convergence. Very high performance across all metrics with precision `~0.99`. Perfectly classifies all normal samples, and has very few false negatives.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run6/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run6/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Higher loss plateau. Significant decrease in performance indicated by a drop in recall and precision values. A higher proportion of false negatives is observed.

### Performance Comparison

<img src="results/run6/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** The performance hierarchy is clearly evident. VQC-2 performed the worst, followed by VQC-1, and VQC-3. VQC-4 achieves the best results. The reduction in performance for the first three models is primarily due to their inability to classify anomalous samples with high precision, whereas VQC-4 outperforms in this regard.

---

## Run 7 - Anomaly ratio set to 0.7

**Parameters:** View [Parameters for this run](./experiments/parameters_run7.json)

```
{
    "run": 7,
    "description": "Anomaly ratio set to 0.7",
    "random_seed": 42,
    "n_samples": 1000,
    "anomaly_ratio": 0.7,
    "test_size": 0.3,
    "maxiter": 100
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run7.json)

### Model Performance Summary

<img src="results/run7/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### Exploratory Data Analysis:

#### 1. Pair plot of data feature distribution

<img src="results/run7/Data-Feature-distribution.png" alt="Data Feature Distribution" width="50%" height="50%">

#### 2. Principal Component Analysis

<img src="results/run7/PCA.png" alt="PCA" width="40%" height="40%">

**Observations:** A greater proportion of samples are now generated as anomalous. The non-anomalous cluster decreased in size, while the anomalous cluster became more densely populated.

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run7/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run7/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Model converges to a higher loss point although there is an increase in performance across all evaluation metrics with precision `~0.75`. Number of false negatives significantly reduced.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run7/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run7/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Unlike all previous cases the model now biases towards the anomalous samples. Classifies all the anomalous samples correctly with a higher proportion of false positives.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run7/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run7/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Significantly better model convergence. Very high performance across all metrics with accuracy `~0.99`. Perfectly classifies all normal samples, and has very few false negatives.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run7/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run7/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Higher loss plateau. Drastic decrease in performance across all evaluation metrics. A higher proportion of false negatives is observed.

### Performance Comparison

<img src="results/run7/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** VQC-2 is the least performant followed by VQC-4. When the models are trained with a higher proportion of anomalous samples, VQC-1 performs better than VQC-4. VQC-3 outperforms all the models with near perfect classification.

Varying the ratio of anomalous samples revealed distinct trends in the strengths and weaknesses of each model. While VQC-3 had robust classification response in all scenarios, the other models had a tendency of biasing towards either one of the class. For example, VQC-4 performed better in the case of lesser anomalous samples but the performance degraded significantly when a higher proportion of anomalous samples were used.

---

## Run 8 - Test set size set to 0.2

**Parameters:** View [Parameters for this run](./experiments/parameters_run8.json)

```
{
  "run": 8,
  "description": "Test set size set to 0.2",
  "random_seed": 42,
  "n_samples": 1000,
  "anomaly_ratio": 0.5,
  "test_size": 0.2,
  "maxiter": 100
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run8.json)

### Model Performance Summary

<img src="results/run8/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run8/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run8/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Increase in training time. Smoother and more consistent descent as seen from the loss curve. Slight decrease in performance across all evaluation metrics with accuracy `~0.67`.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run8/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run8/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Performance is similar to baseline.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run8/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run8/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Decrease in performance compared to runs with varied anomaly ratio. Increase in the number of false negatives.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run8/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run8/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Less stable training and convergence as seen from the loss curve. There is a slower progress and higher loss plateau. Marginal performance gains across all evaluation metrics with accuracy `~0.84`.

### Performance Comparison

<img src="results/run8/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** Performance hierarcy remains similar to previous runs. Increase in the number of training samples has a stabilising effect on model training for VQC-1 and VQC-2.

---

## Run 9 - Test set size set to 0.4

**Parameters:** View [Parameters for this run](./experiments/parameters_run9.json)

```
{
    "run": 9,
    "description": "Test set size set to 0.4",
    "random_seed": 42,
    "n_samples": 1000,
    "anomaly_ratio": 0.5,
    "test_size": 0.4,
    "maxiter": 100
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run9.json)

### Model Performance Summary

<img src="results/run9/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run9/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run9/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Slightly higher loss plateau. Slight decrease in performance across all evaluation metrics with accuracy `~0.7`.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run9/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run9/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Reduced F1-Score `0.37`. High proportion of false negatives maintained.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run9/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run9/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Decrease in performance compared to runs with varied anomaly ratio. Increase in the number of false negatives.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run9/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run9/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Less stable training and convergence as seen from the loss curve. There is a slower progress and higher loss plateau. Decrease in performance across all evaluation metrics. Higher proportion of false negatives.

### Performance Comparison

<img src="results/run9/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** Performance hierarcy remains similar to previous runs. Decrease in the number of training samples reduces model performance in all cases.

---

## Run 10 - Max training iterations set to 50

**Parameters:** View [Parameters for this run](./experiments/parameters_run10.json)

```
{
    "run": 10,
    "description": "Max training iterations set to 50",
    "random_seed": 42,
    "n_samples": 1000,
    "anomaly_ratio": 0.5,
    "test_size": 0.3,
    "maxiter": 50
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run10.json)

### Model Performance Summary

<img src="results/run10/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run10/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run10/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Decrease in performance across all evaluation metrics with accuracy `0.67`.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run10/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run10/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Performance similar to baseline.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run10/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run10/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Decrease in performance compared to runs with varied anomaly ratio. Increase in the number of false negatives.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run10/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run10/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Less stable training and convergence is not optimal. There is a higher loss plateau. Decrease in performance across all evaluation metrics. Higher proportion of false predictions (positive and negative).

### Performance Comparison

<img src="results/run10/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** Performance hierarcy remains similar to previous runs. Reducing the number of iterations does not guarantee a more generalized decision boundary. Model performance is either similar or degraded, as in the case of VQC-4.

---

## Run 11 - Max training iterations set to 200

**Parameters:** View [Parameters for this run](./experiments/parameters_run11.json)

```
{
    "run": 11,
    "description": "Max training iterations set to 200",
    "random_seed": 42,
    "n_samples": 1000,
    "anomaly_ratio": 0.5,
    "test_size": 0.3,
    "maxiter": 200
}
```

**Metrics JSON:** View [Metrics for this run](./experiments/metrics_run11.json)

### Model Performance Summary

<img src="results/run11/Performance-summary-of-all-models.png" alt="Summary" width="50%" height="50%">

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run11/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run11/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="40%" height="40%">

**Observations:** Performance similar to baseline with accuracy `0.72`.

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run11/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run11/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="40%" height="40%">

**Observations:** Performance similar to baseline.

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run11/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run11/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="40%" height="40%">

**Observations:** Performance similar to `100` optimizer iterations.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run11/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="40%" height="40%">

#### Evaluation Metrics

<img src="results/run11/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="40%" height="40%">

**Observations:** Performance degradation as the loss curve converges to a higher point compared to the baseline. Although, the overall performance of the model is better than the `50` optimizer iteration run.

### Performance Comparison

<img src="results/run11/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="50%" height="50%">

**Interpretation of results:** Performance hierarcy remains similar to previous runs. Increasing the number of iterations from `100` does not guarantee better convergence. Model performance is mostly similar.

---

## Summary of experiments

| Run | Parameter Changed | Value            |
| --- | ----------------- | ---------------- |
| 1   | Baseline          | Default Settings |
| 2   | random_seed       | 7                |
| 3   | random_seed       | 100              |
| 4   | n_samples         | 500              |
| 5   | n_samples         | 2000             |
| 6   | anomaly_ratio     | 0.3              |
| 7   | anomaly_ratio     | 0.7              |
| 8   | test_size         | 0.2              |
| 9   | test_size         | 0.4              |
| 10  | maxiter           | 50               |
| 11  | maxiter           | 200              |
