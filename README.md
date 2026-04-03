# 🛡️ Cyber Threat Analysis: Uncovering Stealth Infiltration vs. Automated Botnets

## 📑 Executive Summary
This data analysis project investigates massive network telemetry to solve a critical challenge in Security Operations Centers (SOCs): analyst alert fatigue. 

Through Exploratory Data Analysis (EDA), we identified a fundamental contradiction in threat behavior: automated botnets rely on rigid timing, while human-driven infiltrations actively mimic normal data volume. 

Using PySpark and Machine Learning (Autoencoders, LightGBM) as our analytical engines, we validated this hypothesis, ultimately increasing infiltration detection precision from a 4% baseline to 43%. 

This repository details the full analytical process, from initial EDA to the extraction of actionable, rule-based recommendations for security stakeholders.

---

## 📌 Project Overview

### 💼 The Analytical Problem & Business Objective
Security Operations Centers (SOCs) are overwhelmed by false-positive alerts. 

The root of this problem is analytical: legacy systems treat all "anomalies" as the same. 

However, our initial analysis revealed a bifurcated threat landscape:
1. **The Rigidity Paradox:** Botnets create massive noise through rigid, robotic beaconing.
2. **The Mimicry Trap:** Stealthy Infiltration attacks actively mimic normal human network volume to evade standard spatial filters.

**The Objective:** To conduct a rigorous data analysis that separates these opposing threat profiles, identify the true behavioral drivers of stealth infiltration, and provide SOC management with data-backed recommendations to improve alert fidelity.

### 📂 Data Sources & Analytical Preprocessing
We analyzed a highly imbalanced network flow dataset containing Benign traffic, Botnet beaconing, and Infiltration attacks. 
* **Data Engineering (AWS & PySpark):** Ingested Parquet files and extracted an isolated core of 8 behavioral features and 6 raw TCP flags to ensure our analysis was free of collinearity noise.
* **Temporal Aggregation:** Because our EDA proved that stealthy attacks are invisible at the single-packet level, we engineered 60-second rolling windows to analyze connection density over time.

### 🛠️ Analytical Methodology & ML Integration
We utilized machine learning not as a black-box product, but as an analytical tool to test our behavioral hypotheses:
* **Unsupervised Analysis (Autoencoder):** We trained a neural network strictly on timing sequences. This confirmed that while timing anomalies easily expose Botnets, Infiltration traffic mathematically overlaps with normal human timing, requiring a different analytical approach.
* **Predictive Analysis (LightGBM):** We deployed a Cascading LightGBM classifier. By utilizing its leaf-wise tree growth, we successfully drilled into the 4% minority class (Infiltration) to test our engineered temporal features.
* **Interpretative Analysis (SHAP):** We applied Game Theory (SHAP) to deconstruct the ML model, translating complex mathematical predictions back into readable behavioral insights.

### 📊 Key Findings & Business Recommendations
Our analysis yielded the following data-driven insights and operational recommendations:

* **Alert Fidelity Improvement:** The integration of temporal density features increased Infiltration detection precision to 0.43 (with 0.54 Recall) - a 10x predictive lift over the baseline probability.
* **The "Reset" Indicator:** SHAP analysis revealed that an abnormally high `RST Flag Cnt` combined with high temporal density is the strongest indicator of a stealth compromise.
* **Operational Recommendation:** Do not rely solely on the ML model in real-time. SOC teams should convert these analytical insights into static, computationally inexpensive firewall rules (e.g., rate-limiting RST flags per 60-second window) to secure immediate, low-cost protection, while reserving the pipeline for offline retroactive threat hunting.

### ⚠️ Analytical Limitations & Trade-offs
To ensure data integrity and stakeholder trust, we must acknowledge the limitations of this analysis:
* **Infrastructure Latency:** The reliance on 60-second rolling temporal windows introduces a latency floor. Deploying this logic in real-time requires heavy streaming infrastructure (e.g., Apache Flink).
* **Compute Constraints:** Using `.toPandas()` for LightGBM training creates Out-Of-Memory (OOM) risks on large-scale data, requiring a shift to distributed frameworks (SynapseML) for enterprise scaling.
* **Adversarial Adaptation:** Advanced actors who study this analysis could bypass the timing filters by injecting random "jitter" into their malware, requiring future analysis to focus on adversarial noise injection.synthetic noise into the baseline data.
