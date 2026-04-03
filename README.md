# 🛡️ Hybrid Cyber Threat Detection: Hunting Stealth Infiltration & Botnets

## 📑 Executive Summary
This project develops a machine learning pipeline designed to improve alert fidelity in Security Operations Centers (SOCs). By processing imbalanced network telemetry, the project addresses the inherent complexity of modern threat landscapes, where automated botnets and stealthy human-driven infiltrations present completely opposing behavioral signatures. Using PySpark for temporal feature engineering, a Deep Learning Autoencoder for timing analysis, and a Cascading LightGBM classifier, the system isolates automated botnets and increases the detection precision of stealthy Infiltration attacks from a 4% baseline to 43%. The repository includes full exploratory analysis, model benchmarking, and actionable operational recommendations.

---

## 📌 Project Overview

### 💼 The Business Problem
Security Operations Centers (SOCs) frequently manage high volumes of false-positive alerts. The root of this problem lies in the bifurcated nature of network anomalies: high-volume, automated Botnets create massive noise through rigid beaconing, while low-volume Infiltration attacks actively mimic normal human network behavior. This structural contradiction makes flat, single-model threat detection highly ineffective. The objective of this project is to build a hierarchical ML architecture capable of addressing both opposing threat profiles, improving the detection of stealthy Infiltration traffic while keeping false positives manageable for security analysts.

### 📂 Data Sources & Preprocessing
We processed highly imbalanced network flow datasets containing Benign traffic, Botnet beaconing, and Infiltration attacks. 
* **Data Engineering (PySpark):** Ingested Parquet files and extracted an isolated core of 8 behavioral features and raw TCP flags to prevent data leakage.
* **Temporal Engineering:** Engineered 60-second rolling windows partitioned by Destination Port to capture connection density over time, based on the finding that stealthy attacks are difficult to detect at the single-packet level.

### 🛠️ Methodology & Machine Learning Approach
* **Feature Extraction (Autoencoder):** Trained a 3-feature Deep Neural Network Autoencoder strictly on timing sequences to create a `Timing_Anomaly_Score`, avoiding payload volume data to prevent mimicry.
* **Cascading Supervised Architecture:** * **Stage 1:** An initial XGBoost model focused exclusively on isolating rigid botnet beaconing.
  * **Stage 2:** A LightGBM classifier analyzing the remaining traffic, utilizing leaf-wise tree growth and a tuned `scale_pos_weight` parameter to address the 4% minority class (Infiltration).
* **Explainable AI (XAI):** Applied SHAP to deconstruct the final LightGBM model, ensuring the features driving the predictions are transparent.

### 📊 Key Insights & Recommendations
* **Alert Fidelity Improvement:** The tuned LightGBM architecture achieved a Precision of 0.43 and a Recall of 0.54 for Infiltration attacks. This provides a 10x predictive lift over the baseline probability, helping focus analyst attention on higher-confidence alerts.
* **Actionable TCP Intelligence:** SHAP analysis revealed that high `RST Flag Cnt` combined with temporal density are strong indicators of compromise. 
* **Operational Recommendation:** SOC teams should convert these SHAP insights into static, computationally inexpensive firewall rules (e.g., rate-limiting RST flags per minute) while using the heavier ML pipeline for retroactive, offline threat hunting.

### ⚠️ Operational Limitations & Trade-offs
To maintain transparency, it is important to acknowledge the deployment realities of this architecture:
* **Infrastructure Latency:** Reliance on 60-second rolling temporal windows introduces a latency floor. **Mitigation:** Deploy Apache Kafka & Flink for real-time streaming, accepting higher infrastructure costs.
* **Compute & Memory Scaling:** Using `.toPandas()` for LightGBM training creates Out-Of-Memory (OOM) risks on large-scale data. **Mitigation:** Transition the final classifier to a fully distributed framework (e.g., SynapseML) for cluster-wide scalability.
* **Adversarial Evasion:** Advanced actors could bypass the Autoencoder by injecting random "jitter" into their malware. **Mitigation:** Utilize adversarial training by injecting synthetic noise into the baseline data.
