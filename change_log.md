# Change log

### This file records changes made to the original "Quantum_Based_Anomaly_Detection_VQC 2026" file in the working copy

Changes were made primarily due to three main reasons:

- Removal of student demonstrations to focus notebook execution on VQC model training and evaluation
- Removal of redundant code and re-organizing sections
- Bug fixes

The following records the details of the changes made in the working copy:

- All student demonstrations were removed except for the 'Additional Visualizations' section was.
- The VQC model 1 was trained using the `SPSA` optimizer in the original code, the descriptive markdown for the model was updated to reflect this as it instead mentioned `COBYLA`.
- Sections were re-organized without interrupting the model training pipelines. For example, exploratory visualizations, correlation heatmap of features and PCA are now grouped towards the start of the notebook immediately after the data preprocessing pipeline. Similary, sections on misclassification analysis, summary of results, and additional visualizations are grouped towards the end of the notebook. The re-organization also included removal of redundant code blocks.
- The `EstimatorQNN` used along with `NeuralNetworkClassifier` outputs values in the set [-1, 1], while the synthetic network data labels normal as '0' and anomalous as '1'. This mismatch produced incorrect confusion matrix and standard metric evaluations. The mismatch was fixed by replacing the '0' label with a '-1' during training input and converting '-1' to '0' in the output during evaluation.
- The Second Quantum model: `PauliFeatureMap` + `TwoLocal` + `SPSA`, trained with `SamplerQNN` and `NeuralNetworkClassifier` was successfully trained on the local machine. Thus the placeholder classifier along with the synthetic data made using sklearn's `make_classification` was removed and the actual model was evaluated after training on the synthetic network data.
- The callback issue in the Third Quantum Model: `ZFeatureMap` + `RealAmplitudes` + `ADAM` was fixed by importing `ADAM` from the updated path `from qiskit_machine_learning.optimizers import ADAM` rather than `from qiskit_algorithms.optimizers import ADAM` and passing the callback to the `NeuralNetworkClassifier`. Thus loss values for the model training were successfully plotted.
- The `cobyla_callback` in the Fourth Quantum Model manually computed `binary_cross_entropy_loss`. However, the `NeuralNetworkClassifier` with the `EstimatorQNN` uses the mean `squared_error` loss by default, thus the callback was changed to reflect this.
- Bug fixes were made in the misclassification analysis section's code to properly output and plot misclassified points.
