# Quantum-Based Anomaly Detection in Network Traffic Using Qiskit’s Variational Quantum Classifier

This repository contains my implementation, experiments and run logs for the **Quantum-Based Network Traffic Anomaly Detection** project using Qiskit's Variational Quantum Classifier (VQC).

**Project Description:** This project studies the use of Qiskit’s Variational Quantum Classifier to perform supervised binary classification on a synthetic dataset representing network traffic with labeled anomalies. Each data sample corresponds to a one second snapshot of simplified network activity and is described by features such as packets per second, bytes per second, packet drop rate, and protocol identifier. The dataset is balanced between normal and anomalous traffic, where anomalous samples capture characteristic behaviours including traffic bursts, sudden protocol shifts, and sustained high drop rates. The work involves classical preprocessing and feature scaling, quantum feature encoding using a ZZFeatureMap, and training a fixed variational model composed of a feature map and RealAmplitudes ansatz. Model performance is evaluated on a held out test set using accuracy and confusion matrix metrics, with supporting visualizations used to interpret convergence behaviour and misclassifications.

---

## Project Overview

- **Technique**: Variational Quantum Classifier (VQC) with `ZZFeatureMap` + `RealAmplitudes` ansatz
- **Framework**: Qiskit + qiskit-machine-learning
- **Goal**: Demonstrate reliable quantum simulation workflows, result interpretation, and clear documentation

All experiments are run on local simulators.

---

## Setup Instructions

### Recommended: Local Setup with Anaconda

1. **Clone the repository**

   ```bash
   git clone https://github.com/Mir-Ilham/SQA-2026-Quantum-Based-Network-Anomaly-Detection-VQC.git
   cd SQA-2026-Quantum-Based-Network-Anomaly-Detection-VQC
   ```

2. **Create and activate a new conda environment**

   ```bash
   conda create -n qiskit-anomaly python=3.11
   conda activate qiskit-anomaly
   ```

3. **Install all required packages with exact versions**

   ```bash
   pip install -r requirements.txt
   ```

4. **Launch Jupyter Notebook**

   ```bash
   jupyter notebook
   ```

   or

   ```bash
   python -m notebook
   ```

## How to Run the Project

1. **Open the notebook** `Working_Copy_Quantum_Based_Anomaly_Detection_VQC 2026.ipynb`
2. **Run** the cells from top to bottom.
3. All results, observations, and conclusions are documented in `run_log.md`. Code changes from the original are tracked in `change_log.md`.

## Repo Documentation

1. `run_log.md` is the Main experiment log (what was changed, observed results, and conclusions from each run).
2. `change_log.md` Summary of code modifications made from the original notebook.
3. `results/` folder Contains plots and output from experiments.
