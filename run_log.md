# Run Log - Quantum-Based Anomaly Detection in Network Traffic Using Qiskit’s Variational Quantum Classifier

**Student:** Mir Hasan Mohammad Ilham  
**Project:** Quantum-Based Network Traffic Anomaly Detection using VQC  
**Repository:** https://github.com/Mir-Ilham/SQA-2026-Quantum-Based-Network-Anomaly-Detection-VQC  
**Date started:** 28 March 2026  
**Environment:** Local (Python 3.11.9) + qiskit 2.3.0, qiskit-machine-learning 0.9.0

## Run 1 - Baseline (Working Copy)

**Date:** 28 Mar 2026  
**Changes made:** View [change_log.md](./change_log.md) file for changes made in the original notebook

### Exploratory Data Analysis:

#### 1. Pair plot of data feature distribution

<img src="results/run1/Data-Feature-distribution.png" alt="Data Feature Distribution" width="40%" height="40%">

#### 2. Principal Component Analysis

<img src="results/run1/PCA.png" alt="PCA" width="30%" height="30%">

**Observations:** The pair plot along with the PCA visualization shows very clear separability between normal (blue) and anomalous (orange) traffic, indicating unique behavioral patterns in the dataset. Normal traffic clusters tightly around lower packet rates (~100 packets/sec), lower byte volumes, and very low packet drop rates (~1%), indicating low variance and stable behavior. In contrast, anomalous traffic exhibits significantly higher packet rates and byte volumes with much greater spread, along with elevated and widely dispersed packet drop rates (~0.1). The protocol feature also cleanly separates the classes, with normal traffic concentrated at protocol 1 and anomalies distributed across protocols 2 and 3.

#### 3. Feature Correlation Heatmap

<img src="results/run1/Correlation-heatmap.png" alt="Correlation Heatmap" width="30%" height="30%">

**Observations:** The heatmap shows strong positive correlations (~0.86–0.88) between packet rate, byte rate, and protocol, indicating that higher traffic intensity is consistently linked with protocol shifts. Packet drop rate is also highly correlated (~0.81–0.87) with these features, suggesting congestion-induced packet loss under heavy load.

**Interpretation of results:** From a networking perspective, these patterns suggest that normal traffic corresponds to stable TCP-based communication with consistent throughput and minimal loss, while anomalies reflect bursty, high-intensity traffic often associated with flooding or abnormal transfers. The increased packet drop rates indicate congestion or instability, commonly seen during attacks or misconfigurations. The protocol shift toward UDP/ICMP further reinforces this, as such protocols are frequently used in scanning or DDoS scenarios due to their connectionless nature.

### First Quantum Model: ZZFeatureMap + RealAmplitudes + SPSA

**Key parameters:** `ZZFeatureMap` (reps=2), `RealAmplitudes` (reps=2), optimizer=`SPSA`, maxiter=100, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run1/VQC-Model-1-Loss-Curve.png" alt="Training time and loss curve, VQC-1" width="30%" height="30%">

#### Evaluation Metrics

<img src="results/run1/VQC-Model-1-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-1" width="30%" height="30%">

**Observations:** Stable convergence with decent accuracy (~0.72), but high false negatives (missed anomalies)

### Second Quantum Model: PauliFeatureMap + TwoLocal + SPSA

**Key parameters:** `PauliFeatureMap` (reps=2), `TwoLocal` (reps=2), optimizer=`SPSA`, maxiter=100, qnn=`SamplerQNN`

#### Training time and loss curve

<img src="results/run1/VQC-Model-2-Loss-Curve.png" alt="Training time and loss curve, VQC-2" width="30%" height="30%">

#### Evaluation Metrics

<img src="results/run1/VQC-Model-2-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-2" width="30%" height="30%">

**Observations:** Weakest performance (~0.60), struggles heavily with anomaly detection

### Third Quantum Model: ZFeatureMap + RealAmplitudes

**Key parameters:** `ZFeatureMap` (reps=2), `RealAmplitudes` (reps=3), optimizer=`ADAM`, maxiter=40, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run1/VQC-Model-3-Loss-Curve.png" alt="Training time and loss curve, VQC-3" width="30%" height="30%">

#### Evaluation Metrics

<img src="results/run1/VQC-Model-3-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-3" width="30%" height="30%">

**Observations:** Significant improvement (~0.85), reduced false negatives noticeably.

### Fourth Quantum Model: Deeper Circuit (ZZFeatureMap + RealAmplitudes, reps=4)

**Key parameters:** `ZZFeatureMap` (reps=4), `RealAmplitudes` (reps=4), optimizer=`COBYLA`, maxiter=100, qnn=`EstimatorQNN`

#### Training time and loss curve

<img src="results/run1/VQC-Model-4-Loss-Curve.png" alt="Training time and loss curve, VQC-4" width="30%" height="30%">

#### Evaluation Metrics

<img src="results/run1/VQC-Model-4-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, VQC-4" width="30%" height="30%">

**Observations:** Best performance (~0.89), strong convergence and balanced predictions → deeper circuit + better optimizer yields highest separability capture.

### Classical Model Benchmarks: SVM & RandomForest

#### SVM

**Key parameters:** `kernel='rbf'`, `C=1.0`, `gamma='scale'`

<img src="results/run1/SVM-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, SVM" width="30%" height="30%">

#### RandomForest

**Key parameters:** `n_estimators=100`, `random_state=42`

<img src="results/run1/Random-Forest-Evaluation-Metrics-and-CM.png" alt="Evaluation Metrics, RandomForest" width="30%" height="30%">

**Observations:** Both `RandomForest` and `SVM` (RBF kernel) achieve perfect performance (100% across all metrics), indicating that the dataset is highly separable in feature space. This suggests that classical models can easily learn the decision boundary due to strong feature correlations and clear distribution shifts, highlighting that the current data setup is relatively simple.

### VQC Model-4 Misclassification Analysis

#### Misclassification analysis via PCA

<img src="results/run1/VQC-Model-4-Misclassification-analysis-PCA.png" alt="Misclassification analysis PCA" width="30%" height="30%">

#### Misclassifications by true label

<img src="results/run1/VQC-Model-4-Misclassifications-by-true-label.png" alt="Misclassifications by true label" width="30%" height="30%">

**Observations:** The PCA plot shows that most misclassifications occur within the anomalous cluster, particularly in regions where data points are more dispersed. Normal samples remain tightly clustered and are almost perfectly classified, reflecting strong separability for that class. The misclassification count further confirms this imbalance, with significantly more errors in anomalies than normal traffic.

### Performance Comparison of Models and Summary

#### Performance Comparison

<img src="results/run1/Performance-comparison-of-all-models.png" alt="Performance Comparison" width="40%" height="40%">

#### Summary

<img src="results/run1/Performance-summary-of-all-models.png" alt="Summary" width="40%" height="40%">

**Interpretation of results:** The comparison shows a performance hierarchy where VQC-2 performs the worst, followed by VQC-1, while VQC-3 and especially VQC-4 (deep circuit) show strong improvements, indicating that increased circuit depth and better optimizers significantly enhance learning capacity. However, both classical models (SVM and Random Forest) achieve perfect scores across all metrics, confirming that the dataset is linearly/nonlinearly separable enough for classical methods to fully capture. Overall, this highlights that while VQCs can approximate the decision boundary with increasing expressivity, they still lag behind classical models in efficiency and optimization on this dataset.

---
