# HealthClaim Standardizer: Multi-Source Healthcare Data Standardization Pipeline

## 🎯 Project Objective

The **HealthClaim Standardizer** project is designed to address a major challenge in Healthcare Data Engineering: **standardizing raw data from multiple customers** (Multi-Customer Data Ingestion) into a single data warehouse.

The core goal is to create a unified raw layer (Standard Schema) in the Data Warehouse, allowing analytics teams to run queries and analytical models across the entire dataset without worrying about the original source format.

<img width="647" height="341" alt="hide" src="https://github.com/user-attachments/assets/1edbf7a4-dc02-4594-a2b6-abc3a3248f77" />

---

## Datasets

The project uses two main data sources, simulating a real-world healthcare data environment:

1. **CMS Claims Data:**
    * **Source:** Synthetic Medicare Claims and Enrollment Data.
    * **Purpose:** Provides claims and beneficiary information with complex ICD and HCPCS codes, simulating the structure of the CMS Research Identifiable File (RIF).
    * **Link:** [CMS Synthetic Data Collection](https://data.cms.gov/collection/synthetic-medicare-enrollment-fee-for-service-claims-and-prescription-drug-event)

2. **NDC Drug Code Data (National Drug Code):**
    * **Source:** NDC file from OpenFDA (contains information about all FDA-approved drugs).
    * **Link:** [OpenFDA NDC Drug Data](https://download.open.fda.gov/drug/ndc/drug-ndc-0001-of-0001.json.zip)

<img width="600" height="332" alt="data lúc 00 09 08" src="https://github.com/user-attachments/assets/594d209b-7b6a-4374-a224-89851278fc48" />

---

## Architecture & Pipeline (ELT Pipeline)

The system is built on **Apache Airflow** (orchestration), **Snowflake** (Data Warehouse), and **PostgreSQL** (Metastore/Mapping).

### 1. Dynamic Standardization Strategy

To handle multiple source schemas, the pipeline uses a dynamic mapping mechanism, enabling easy integration of new customer data without modifying the Airflow framework:

* **Logic:** When a CSV file enters the system, the Airflow task **uses the file name (`file_name`)** to look up the corresponding entry in the **`customer_sql_mapping`** table (in PostgreSQL).
* **Outcome:** The system automatically identifies and loads the corresponding **transformation SQL script** (`transform_customer_X.sql`).
* **Standardization:** This SQL script is then executed in Snowflake, ensuring that the raw data from that customer is transformed and loaded into a **single unified target schema** (e.g., `HEALTHCARE_DWH.RAW`).

### 2. Data Enrichment

The **`fda_download`** DAG ensures that the NDC drug code data is updated monthly and stored locally. This NDC data is crucial for advanced analytics, as it allows:

* **NDC Translation:** Converting drug codes (NDC) into brand names, active ingredients, and dosage information.
* **Advanced Analytics:** Incorporating detailed drug information into the DWH to support pharmaceutical cost analysis, prescribing trends, and drug utilization studies.

### 3. Unified Raw Data Layer

All source data is stored in a single schema (`HEALTHCARE_DWH.RAW`) with tables following a standardized schema. This creates a clean, reliable foundation for building downstream analytical layers (STANDARD, ANALYTICS).


### **Practical Use Cases for Analysts**

1. **Cost Analysis**: Identify the highest-cost services and procedures using CPT or HCPCS codes.
2. **Population Health**: Segment populations by diagnosis (ICD codes) to identify trends or disparities in care.
3. **Utilization Analysis**: Determine service utilization rates by department (revenue codes) or service type.
4. **Performance Metrics**: Evaluate healthcare provider performance based on DRG benchmarks or CPT-related outcomes.
5. **Fraud Detection**: Identify anomalies in claims data, such as duplicate billing or mismatched codes.

By mastering these concepts, data analysts can extract actionable insights from claims data, helping improve operational efficiency, financial performance, and patient outcomes.
