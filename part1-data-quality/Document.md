# 📈 Data Quality Documentation – Puffy Analytics Pipeline

This document describes the comprehensive data quality checks and cleaning processes applied to the Puffy analytics dataset before building the final data warehouse, attribution models, and machine-learning components. All steps listed below reflect **exactly** the work performed in the preprocessing stage to ensure a traceable, schema-consistent, and analytics-ready foundation.

---

## 1. Data Type Validation & Normalization

This stage focused on establishing **analytical consistency** across all fields.

### 1.1 Checked and Corrected Data Types

Each column was reviewed and corrected for appropriate data type assignment.

* **Special attention was given to:**
    * **Timestamps** (ensuring correct datetime format).
    * **Numeric revenue fields** (ensuring float/decimal precision).
    * **Click ID fields** (ensuring consistent string/object type).
    * **Session and client identifiers** (ensuring consistent string/object type).
* *Outcome: Incorrect types were corrected to ensure reliable calculations and mergers.*

### 1.2 Google 3A / 2A Numeric Cleaning

A robust cleaning logic was applied to handle numeric fields containing non-numeric characters (a common data ingestion issue).

* **Google 3A Logic (Auto-Detect):** Automatically detect and identify non-numeric/invalid characters in numeric columns.
* **Cleaning:** Strip or correct the invalid characters.
* **Google 2A Logic (Conversion):** Convert all cleaned values into proper numerics (e.g., float or integer).
* *Outcome: Numeric columns (like revenue, price, quantities, and certain click IDs) are now fully usable for analysis and modeling.*

---

## 2. Duplicate Detection and Removal

Duplicate rows were detected and safely removed to ensure data integrity at the event and transaction level.

* **Goal:** Ensure **no duplicated events**, **no repeated transaction records**, and **no repeated item rows**.
* **Process:** Deduplication was performed while actively **preserving the earliest valid version** of the record in time.

---

## 3. Column Normalization and Schema Alignment

Standardization was implemented to ensure safe and consistent joining/merging operations.

### 3.1 Column Name Normalization

All column naming was standardized to **snake\_case** for consistency.

* *Examples:* `"client id" \rightarrow "client\_id"`, `"ClientID" \rightarrow "client\_id"`

### 3.2 Equalizing Column Structures Across Files

This step ensured that different imported source files (e.g., CSVs) had identical schemas for reliable unions and joins.

* **Process:**
    * Missing columns were added (filled with `None` or appropriate NULL/NaN marker).
    * Extra, non-essential columns were dropped.
* *Outcome: Final schemas were aligned, preventing accidental data loss or breakage due to mismatched column counts.*

---

## 4. Categorical Sampling & Category Unification

This stage addressed variations in text-based categorical fields to ensure accurate grouping and segmentation.

* **Process:** Categories that represented the same underlying value were unified.
* *Examples:* `"BRAZIL"`, `"Brazil"`, `"BR"` were all unified into `"Brazil"`.
* *Outcome: Cleaner segmentation, more accurate grouping, and prevention of artificially inflated category counts.*

---

## 5. Quality Flags System

A comprehensive quality flag framework was created to automatically detect and identify data anomalies across all events, enabling proactive monitoring.

### Flags Implemented:

* `flag_invalid_event_date`
* `flag_missing_revenue_checkout`
* `flag_revenue_on_non_checkout`
* `flag_transactionid_no_revenue`
* `flag_missing_timestamp`
* `flag_missing_event_name`
* `flag_missing_client_id`
* `flag_missing_page_url_on_view`
* `flag_suspicious_timestamp`
* `flag_empty_utm_fields`
* `flag_invalid_user_agent`

### 5.1 Summary of Issues Identified

The flagging system was immediately utilized to identify existing critical issues:

* **12 rows** where `event = checkout` but `revenue = 0` (a major business issue explaining revenue declines).
* Duplicate transaction IDs.
* Malformed or inconsistent user agents.
* Multiple incomplete or missing UTM fields.
* Missing timestamps and page URLs in some events.
* *Outcome: This system ensures **future monitoring detects problems automatically**.*

---

## 6. Column Parsing & Parameter Extraction

Embedded attributes within columns were parsed and separated to extract useful analytical fields.

* **Examples:**
    * Extracting color, size, and variant information from product strings.
    * Identifying and separating UTM parameters.
    * Extracting click IDs from long source URLs.
    * Splitting product attributes from combined strings (e.g., from an item array).

---

## 7. Color Code Standardization

Raw technical color codes were replaced with actual, readable color names for improved analysis and visualization.

* *Examples:* `"4E8723" \rightarrow "Green"`, `"4689DD" \rightarrow "Blue"`.
* *Outcome: Cleaner grouping, better visualization, and improved categorical encoding for Machine Learning models.*

---

## 8. Removal of Unnecessary Columns

Columns determined to be not relevant to any downstream analysis, reporting, or modeling were safely removed.

* *Benefits:* Minimizing **memory usage**, reducing **noise** in the dataset, and decreasing **complexity** in joins and transformations.

---

## 9. Overall Outcome and Foundation

After completing these robust data-quality tasks, the analytics pipeline achieved its goals:

* **Traceability & Consistency:** The dataset became fully **traceable**, **schema-consistent**, and **analytics-ready**.
* **Issue Resolution:** All major revenue, timestamp, and event issues were identified and addressed **before** modeling began.
* **Future Resilience:** The **Quality Flags System** is now in place for automated daily monitoring and alerting against future anomalies.
* **Reliability:** Joining and merging logic is consistent and safe; categorical values are standardized; and numeric transformations are reliable and validated.

This cleaned and validated dataset now forms the reliable foundation for:

* **Attribution Analysis**
* **Marketing Performance Evaluation**
* **Machine Learning Conversion Prediction**
* **Daily Monitoring and Automated Alerting**