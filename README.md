#Employee Attrition Rate Analysis#

This project analyzes the workforce data from `hr_employee_attrition` using **SQL in Databricks** to determine the core metrics behind employee churn and retention. The objective is to identify key factors influencing employee turnover—such as travel frequency, department structure, distance from home, career longevity, and compensation—to provide actionable insights for HR management.

---

## 📊 Key Findings & Overview

* **Overall Retention:** The company maintains an overall **Retention Rate of 83.88%**, with a **Churn Rate of 16.12%**.
* **Travel Risks:** Employees who **Travel Frequently** show the highest vulnerability, experiencing a **24.91% segment churn rate**.
* **Department Breakdowns:** The **Sales Department** experiences the highest attrition rate at **20.63%**, followed closely by Human Resources at 19.05%.
* **Income Matrix:** Lower-income brackets (`$0 - $5,000` monthly) coupled with high salary hikes paradoxically exhibit high churn (27.78%), suggesting market-rate corrections or baseline pay issues.
* **Proximity Matters:** Over **56% of total churned employees** live within 10 miles from the office, contradicting common long-commute assumptions.

---

## 🛠️ Dashboard Architecture & Metrics

The project includes an interactive Databricks Canvas dashboard configured with key dimensions:

### 1. Retention Overview
* **Widget Type:** Pie Chart (`f34093a3`)
* **Metrics:** Evaluates global macro retention vs churn percentages.

### 2. Business Travel Impact
* **Widget Type:** Stacked Percent Bar Chart (`173ea3f0`)
* **Metrics:** 
  * `Non-Travel`: 8.00% Churn Rate
  * `Travel_Rarely`: 14.96% Churn Rate
  * `Travel_Frequently`: 24.91% Churn Rate

### 3. Department Diagnostics
* **Widget Type:** Bar Chart (`2553eaad`)
* **Metrics:** Breaks down employee volume, churn count, and retain rate across Sales, R&D, and HR.

### 4. Distance From Home Analysis
* **Widget Type:** Pie Chart (`3fb3329a`)
* **Metrics:** Segments churn counts into distance bands (`0-10`, `10-20`, `20-30` miles).

### 5. Career Progression & Education
* **Widget Type:** Bar Chart with Interactive Filters (`5ab8289a`)
* **Metrics:** Correlates `Education` levels against time spent in a single role (`YearsInCurrentRole`). New tenure periods (0-5 years) generally see the highest attrition spikes across education categories.

### 6. Income vs Salary Hike Analysis
* **Widget Type:** Multi-Group Bar Chart (`eca7d480`)
* **Metrics:** Examines the compound relationship of baseline monthly income groups stacked against performance salary hike percentages.


## 🗄️ SQL Core Queries Breakdown

Below are key operational queries executed within the notebook environments:

### Overall Attrition Breakdown
```sql
SELECT
  'ChurnRate' AS RateType,
  ROUND((SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) / COUNT(*)) * 100, 2) AS RateValue
FROM hr_employee_attrition

UNION ALL

SELECT
  'RetainRate' AS RateType,
  ROUND((SUM(CASE WHEN Attrition = 'No' THEN 1 ELSE 0 END) / COUNT(*)) * 100, 2) AS RateValue
FROM hr_employee_attrition;
```

### Travel Segment Impact
```sql
SELECT 
  BusinessTravel, 
  COUNT(*) as Total, 
  SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) as ChurnCount,
  ROUND((SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) / COUNT(*)) * 100, 2) as SegmentChurnRate,
  SUM(CASE WHEN Attrition = 'No' THEN 1 ELSE 0 END) as RetainCount,
  ROUND((SUM(CASE WHEN Attrition = 'No' THEN 1 ELSE 0 END) / COUNT(*)) * 100, 2) as RetainRate
FROM hr_employee_attrition
GROUP BY BusinessTravel;
```

### Income & Comp Hike Intersections
```sql
SELECT
  CASE
    WHEN MonthlyIncome >= 0 AND MonthlyIncome < 5000 THEN '0-5000'
    WHEN MonthlyIncome >= 5000 AND MonthlyIncome < 10000 THEN '5000-10000'
    WHEN MonthlyIncome >= 10000 AND MonthlyIncome < 15000 THEN '10000-15000'
    WHEN MonthlyIncome >= 15000 AND MonthlyIncome < 20000 THEN '15000-20000'
    ELSE '20000+'
  END AS income_group,
  CASE
    WHEN PercentSalaryHike >= 11 AND PercentSalaryHike <= 15 THEN '11-15'
    WHEN PercentSalaryHike >= 16 AND PercentSalaryHike <= 20 THEN '16-20'
    WHEN PercentSalaryHike >= 21 AND PercentSalaryHike <= 25 THEN '21-25'
    ELSE 'Other'
  END AS salary_hike_group,
  COUNT(*) AS Total,
  SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) AS ChurnCount,
  ROUND((SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) / COUNT(*)) * 100, 2) AS AttritionRate
FROM hr_employee_attrition
GROUP BY income_group, salary_hike_group
ORDER BY income_group, salary_hike_group;
```

---

## 🚀 How to Run the Project in Databricks

1. **Data Ingestion:** Upload your dataset and register it as a managed or unmanaged table named `hr_employee_attrition` within your Databricks catalog.
2. **Notebook Execution:** Run the provided SQL scripts cell-by-cell to validate dataset profiles and look for zero-variance columns (e.g., verifying `Over18` field consistencies).
3. **Canvas Generation:** Build a new Databricks Canvas dashboard and bind the queries using the mapped datasets (`1fceeb76`, `b23ebd4a`, etc.) as designated in the layout configurations.
---
