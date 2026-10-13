# Financial Fraud Command Center: SecOps Analytics

## Project Overview
This repository contains an enterprise-grade Power BI command center designed for Security Operations (SecOps) and Risk Management teams. Built on top of a simulated financial transaction database, this dashboard transforms raw telemetry into actionable intelligence, enabling analysts to identify anomalous behavior, track system accuracy, and monitor multi-vector fraud attempts.

## Architecture & Data Pipeline
This reporting layer is designed to sit atop a robust data engineering workflow:
1. **Data Extraction:** Raw transaction logs (based on the PaySim Synthetic Financial dataset) are ingested.
2. **Data Processing:** Processing and normalization handle the large-scale transaction data.
3. **Data Warehousing:** Cleaned data is partitioned and loaded into a relational database (SQLite).
4. **Analytics Layer:** Power BI connects directly to the database via ODBC to render the Command Center.

## Data Modeling: Star Schema
To ensure high performance and efficient DAX calculations, the flat transaction logs were transformed in Power Query into a normalized Star Schema:
* **`Fact_Transactions`**: The central fact table recording measurable events (`amount`, `isFraud`, `isFlaggedFraud`).
* **`Dim_Time`**: A dimension table derived from transaction steps to enable continuous time-series analysis and historical trend tracking.
* **`Dim_Transaction_Type`**: Classifies operations (e.g., `TRANSFER`, `CASH_OUT`, `PAYMENT`) to isolate high-risk vectors.

## Core Features & DAX Implementation
The dashboard features custom DAX measures built to evaluate both financial risk and operational efficiency:

* **Real-Time Threat Tracking:** Monitors baseline transaction volume against confirmed fraud cases.
* **Legacy System Evaluation:** Tracks the True Positive Rate of the legacy automated detection system, highlighting the operational necessity for advanced machine learning interventions.
* **High-Risk Vector Analysis:** Matrix visualizations isolate transaction types with the highest fraud probability.

**Sample DAX Measure: System Flag Accuracy**
```dax
System Flag Accuracy = 
DIVIDE(
    CALCULATE(
        [Total Transactions], 
        'Fact_Transactions'[isFraud] = 1, 
        'Fact_Transactions'[isFlaggedFraud] = 1
    ),
    [Confirmed Fraud Cases],
    0
)
```

