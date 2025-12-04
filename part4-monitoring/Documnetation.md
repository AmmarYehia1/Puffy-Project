## 📊 Production Monitoring Overview

The production monitoring script executes a set of **lightweight daily checks** to ensure the main warehouse tables are in a **healthy state** before being used for analytics or reporting. The output is a compact table of **PASS / WARN / FAIL** results, suitable for analysts or automated alerting systems.

---

## 🔍 What the Monitoring Covers

The checks focus on data quality, integrity, and consistency across several critical areas:

* **Data Freshness:**
    * Checks the timestamp of the **most recent event**.
    * **Goal:** Flag unusually old data immediately to catch **ingestion failures** early.

* **Event Volume Patterns:**
    * Compares **today’s event count** with the previous week.
    * **Goal:** Detect large swings (too low or too high) that suggest upstream issues like **API outages, broken trackers, or duplicate loads**.

* **Field Completeness:**
    * Checks for unexpected **null rates** in essential columns.
    * **Focus Areas:** Identifiers ($\text{client\_id}$, $\text{transaction\_id}$), time fields, and revenue fields.
    * **Goal:** Prevent broken metrics or incorrect reporting caused by **missing essential values**.

* **Revenue Consistency:**
    * Validates for **negative revenue**, mismatches between item-level and order-level revenue.
    * Recalculates line-revenue ($\text{quantity} \times \text{price}$) to catch inconsistencies.
    * **Goal:** Prevent **incorrect revenue numbers** in dashboards and attribution models.

* **Referential Consistency:**
    * Checks that every session maps to a **known user** and every order item belongs to a **valid order**.
    * **Goal:** Identify misalignments that suggest **partial loads or incorrectly joined datasets**.

* **Tracking Coverage:**
    * Calculates the share of orders that include a **click ID or UTM metadata**.
    * **Goal:** Flag drops in this percentage, indicating **tag failures or ingestion layer issues**.

* **Daily KPIs:**
    * Provides a quick **temperature check** by surfacing core metrics for the monitoring date: sessions, orders, revenue, conversion rate, and AOV (Average Order Value).
    * **Goal:** Quick validation that numbers **“look normal.”**

---

## 🚨 Alerting

If the script finds anything unusual (a $\text{WARN}$ or $\text{FAIL}$ result), it prepares a **short summary** and can send an email to the data team. This mechanism is designed to be **minimal** and non-disruptive, allowing it to run as part of a morning job without creating excessive noise.

---

## ✅ Why This Approach Works Well in Operations

This setup is effective because it:

* **Focuses on Critical Signals:** Prioritizes whether data is **fresh, complete, internally consistent, and reliable** for daily reporting.
* **Uses Simple Thresholds:** Thresholds are wide enough to **avoid constant alerts** but tight enough to **catch real issues**.
* **Is Quick and Interpretable:** Each check is designed for **fast execution** and **easy interpretation**, enabling the team to resolve issues quickly without extensive diagnostics.
* **Is Lightweight:** It covers the essentials of a daily data quality routine **without becoming heavy or brittle**, making it practical for normal warehouse operations.