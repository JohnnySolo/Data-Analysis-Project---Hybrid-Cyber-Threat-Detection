# 🛡️ Hybrid Cyber Threat Detection: Hunting Stealth Infiltration & Zero-Day Botnets

## 📌 Project Overview

This project builds an advanced, end-to-end Machine Learning pipeline to detect highly sophisticated cyber threats within massive network telemetry. By combining distributed data engineering, unsupervised deep learning, and a hierarchical supervised architecture, we move beyond generic "anomaly detection" to explicitly hunt stealthy Infiltration attacks while prioritizing Security Operations Center (SOC) business metrics.

### 💼 The Business Problem & Objective

Modern Security Operations Centers (SOCs) are drowning in "Alert Fatigue." Legacy unsupervised systems (like Isolation Forests) trigger thousands of false alarms daily because they fail to understand two modern threat realities: Botnets form dense, identical clusters, and Infiltration attacks actively mimic normal human network volume. 

The objective of this project is to build a hybrid ML architecture that eradicates zero-day botnets and successfully isolates stealthy Infiltration traffic, achieving a massive predictive lift in Precision without creating unmanageable false positives for security analysts.

### 📂 Data Sources & Preprocessing

We processed massive, highly imbalanced network flow datasets containing Benign traffic, Botnet beaconing, and stealthy Infiltration attacks. 

* **Distributed Data Engineering (PySpark):** * Ingested and processed gigabytes of Parquet files using PySpark.
  * Extracted an isolated, scientifically orthogonal core of 8 behavioral features and raw TCP flags to prevent collinearity noise and data leakage.
  * **Temporal Engineering:** Built 60-second rolling windows partitioned by Destination Port to mathematically capture "Campaign Bursts" and sequential attacker density, proving that stealthy attacks are invisible at the single-packet level.

### 🛠️ Methodology & Machine Learning Approach

This project was executed using the **PEAK Framework** (Prepare, Execute, Act, Knowledge), deploying a highly specialized ML architecture:

* **Unsupervised Deep Learning (Feature Extraction):** Trained a 3-feature Deep Neural Network **Autoencoder** strictly on timing sequences (blinding it to payload volume). This created a highly sensitive `Timing_Anomaly_Score` tripwire that could not be tricked by volume mimicry.
* **Cascading Supervised Architecture (Easy-First):** * **Stage 1 (The Bot Hunter):** An initial XGBoost model hyper-focused on isolating and removing rigid, robotic botnet beaconing.
  * **Stage 2 (The Infiltration Hunter):** A highly sensitive **LightGBM** classifier that analyzes the remaining traffic. We utilized LightGBM's leaf-wise tree growth and tuned the `scale_pos_weight` parameter to aggressively hunt the 4% minority class (Infiltration) without causing false-positive blowouts.
* **Model Benchmarking:** Pitted XGBoost against LightGBM in the Stage 2 architecture, proving that LightGBM's leaf-wise growth yields a strict Pareto improvement for complex mimicry patterns.
* **Explainable AI (XAI):** Deployed **SHAP (Game Theory)** to deconstruct the final LightGBM model, ensuring the AI's decisions are transparent and actionable for human security analysts.

### 📊 Key Insights & SOC Business Value

By shifting the focus from misleading "Global Accuracy" to strict Precision/Recall evaluation, the pipeline delivered measurable improvements across core SOC KPIs:

* **Alert Fidelity & Analyst ROI (10x Lift):** Infiltration traffic represents just ~4.1% of the baseline dataset (a random guess yields 0.04 precision). Our tuned LightGBM architecture achieved a Precision of 0.43 and a Recall of 0.54. This **10x predictive lift** catches the majority of stealthy attacks while keeping false alarms highly manageable, saving hundreds of wasted analyst investigation hours.
* **The Mimicry Trap Exposes the Need for Temporal Analysis:** Our SHAP and ECDF analysis proved that spatial outlier detection fails because Infiltration perfectly mimics human payload sizes. The top 3 most critical features for catching these attacks were the 60-second rolling windows we engineered in PySpark. 
* **Actionable Intelligence & MTTR Reduction:** The SHAP analysis eradicated the "Black Box" problem. It revealed that high `RST Flag Cnt` combined with temporal density are the strongest indicators of compromise (IoCs). Instead of blindly trusting a model, SOC analysts can use these transparent insights to instantly write targeted, stateful firewall rules, drastically reducing the Mean Time to Respond (MTTR).
