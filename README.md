# Payroll Project and Dashboard [(Live Report Publish)](https://app.powerbi.com/view?r=eyJrIjoiMmViZDYzYWUtZjVmMy00NDc4LWI2MDgtOGFlYmJhODg5NDViIiwidCI6IjZhNjhlMmQxLWQ4OGQtNDEyYi1iOTgyLWQ0YWVkNWY1MTcxNiJ9)
This project is a system that helps businesses in tracking the Payroll system. The SQL script transforms raw data into datasets with appropriate format and no conflictions when reading. Then, I used Power BI Dashboard to visualize and draw meaningful insights. These findings will determine actions to either embracing it or reduce it.

## Table of Contents
- [1) Datasets](#1-datasets)
- [2) Dashboard Overview](#2-dashboard-overview)
- [3) Data Transformation](#3-data-transformation)
- [4) Dashboard Relationships](#4-dashboard-relationships)
- [5) Insights](#5-insights)
- [6) Conclusion](#6-conclusion)
---

## 1) Datasets:

The datasets includes 14 files that are in .csv format:
- **Allowance**: Extra payments made to employees within specific periods. There are
two main types: Laundry Allowance and First Aid Allowance.
- **Bonus**: Extra rewards for employees within specific events such as Retention Bonus,
Christmas Bonus, Performance bonus, etc.
- **Combined holiday**: List of holiday plans that were implemented. For example, if the
Christmas falls on Sunday, there will be extra day-offs for the following days as
additional holidays.
- Contract details: Contains information about the employees such as job type, title,
active status, pay rate, etc.
- **Date**: A **manual created** dataset created to help retrieving the chronological time.
- **Employee details**: List of employees along with their personal information.
- **Employee leaves**: List of leaves recorded.
- **Junior pay rates**: Wages for employees within specific age group.

## 2) Dashboard Overview:

### Dashboard Preview Screenshot:
These are my dashboard screenshots. Further Report can be view in here: [Report Link](https://github.com/minhD03/Payroll-Project/blob/9531a8b92243f0e47f6c6749846aaf5dfaa170df/Payroll%20Report-%20Nhat%20Minh%20Dang.pdf)

![alt text](https://github.com/minhD03/Payroll-Project/blob/34def8fb7416c1a571bd876d1d7e0f672d19944f/Images/Dashboard%201.png)

![alt text](https://github.com/minhD03/Payroll-Project/blob/34def8fb7416c1a571bd876d1d7e0f672d19944f/Images/Dashboard%202.png)
---

## 3) Data Transformation:

![alt text](https://github.com/minhD03/Payroll-Project/blob/686a75198d95fc25bf522e353ad8ec660bc325d0/Images/Medallion%20Architecture.jpg)

In this project, I will use three data schemas: Landing, Staging and Mart. These schemas represent three steps in Medallion Architecture. Medallion architecture is a powerful data design pattern used in modern Lakehouse systems to progressively refine and organize data across three distinct layers Bronze, Silver and Gold,each representing a step in data quality and usability. At its foundation, the Bronze layer ingests raw, unprocessed data from diverse sources such as CRM, ERP or LOB systems, preserving its original form for traceability and historical analysis. This raw data then flows into the Silver layer, where it undergoes cleaning, validation and enrichment, removing duplicates, standardizing formats and integrating disparate datasets to create a more coherent and reliable view. Finally, the Gold layer transforms this refined data into business, ready assets through dimensional modelling, aggregation and domain-specific enhancements, making it ideal for executive dashboards, machine learning models and advanced analytics. This tiered approach not only ensures data integrity and governance but also enables modular development, scalability and efficient collaboration across data engineering, analytics and decision-making teams. By separating concerns and incrementally improving data quality, medallion architecture empowers organizations to build robust, flexible pipelines that support both operational reporting and strategic insights.

Because the Date table was created individually, it was put into the Mart layer. For other files, they were imported into Landing layer.

After importing datasets into SQL Server, I divided the tables into **Dimension Table** and **Fact Table**. In a data warehouse, **Dimension Table** provide descriptive context like employee details, contract terms or pay rate categories that help slice and filter data. They’re typically wide and relatively static. **Fact Table**, on the other hand, store measurable events such as allowances, bonuses, timesheets or rosters and link to dimensions using surrogate keys. These tables are optimized for aggregations, reporting and analytical queries.

In the SQL script, I started by transforming raw landing data into clean, structured staging tables, selecting key columns across HR, payroll and roster files. Then, I standardized formats, renamed fields and mapped identifiers like employee_code to employee_id. In addition, in the marts layer, I built dimension tables by hashing keys (e.g., employee_pk, contract_pk), converting percentages to multipliers and aggregating pay periods. For fact tables, I joined staging data with dimensions using surrogate keys and date logic, ensuring each record was contextualized by contract, employee and pay period. This layered approach supports robust analytics and executive reporting.

## 4) Dashboard Relationships:

When creating Power Bi Dashboard, these are the relationships that I connected:
| From Table (Column)                  | Relationship Type | To Table (Column)                     |
|-------------------------------------|-------------------|---------------------------------------|
| `dim_date(date_pk)`  | One to Many   |  `fact_allowances (allowance_start_date_fk)`    |
| `dim_employees(employee_pk)`     | One to Many     |    `fact_allowances(employee_fk)`    | 
| `dim_pay_period(pay_period_pk)`| One to Many      | `fact_allowances(pay_period_fk)`         | 
| `dim_date(date_pk)`| One to Many      | `fact_bonuses(bonus_date_fk)`         | 
| `dim_eployees(employee_pk)`      | One to Many     | `fact_bonuses(employee_fk)`      | 
| `dim_pay_period(pay_period_pk)`| One to Many      | `fact_bonuses(pay_period_fk)`         | 
|`dim_employees(employee_pk)`  | One to Many      |      `fact_employee_leaves(employee_fk)`   | 
|  `dim_pay_period(pay_period_pk)` | One to Many      |   `fact_employee_leaves(pay_period_fk)`     | 
| `dim_employees(employee_pk)`     | One to Many     |    `fact_roster(employee_fk)`    | 
|  `dim_pay_period(pay_period_pk)` | One to Many      |   `fact_roster(pay_period_fk)`     | 
| `dim_date(date_pk)`  | One to Many   |  `fact_roster(work_date_fk)`    |
| `dim_employees(employee_pk)`     | One to Many     |    `fact_timesheet(employee_fk)`    | 
| `dim_pay_period(pay_period_pk)`| One to Many      | `fact_timesheet(pay_period_fk)`         | 
| `dim_contract(contract_pk)`| One to Many      | `fact_timesheet(contract_fk)`         |
| `dim_date(date_pk)`| One to Many      | `fact_timehseet(timesheet)_transition_date_fk)`         | 
| `dim_date(date_pk)`| One to Many      | `fact_employee_leaves(leave_start_date_fk)`          | 

# 5) Insights:

## a) Workforce Composition & Contracts
- Employment types (Part-time, Full-time, Casual) are evenly split at ~33% each.
- Casual contracts dominate overall (50k vs 20k full-time), with Full-time demand rising post-2025.
- IT Support Specialist, Security Guard and Software Developer make up 19% of all roles.
- IT Support is mostly part-time/casual with demand only emerging from 2025 (short-term signal).
- Software Developer relies heavily on casual staff (~half) but delivers high payroll value.
- Administrative Assistant shows full-time dominance and stable, low-fluctuation payroll.
- 70% of contracts are active, 24% expired, 6% terminated.

## b) Payroll Risk & Financial Trends
- Overpayments ($5.39M) far outweigh underpayments ($34.8K), a major financial exposure.
- Paid amounts surged post-June 2024 while Mandatory amounts rose only modestly (disproportionate growth).
- 2022–2024 saw fluctuations (Paid: 0.06M–0.08M; Mandatory: 0.04M–0.05M) tied to COVID-era disruption.
- Expired/terminated contracts pose ongoing payroll discrepancy risk.

## c) Employee Workload & Well-being
- Company-wide: ~30k overtime hours vs 2.26k undertime hours over 5 years, alongside ~1M bonus cards and 26.5k allowance cards.
- 2025 saw a spike to 390 overtime hours in just four months, possible burnout/staffing gap signal.
- Individual patterns vary widely:
  - Aaron Morales: balanced overtime/undertime with regular leave (healthy benchmark).
  - Rober: notable overtime-undertime gap, suggesting possible underutilization.
  - Eric: ~740 overtime hours with almost no leave, high burnout risk.

---

# 6) Conclusion
Overall, the workforce shows a structurally balanced contract mix but carries concentrated risk in a small set of payroll-sensitive roles and a heavy reliance on casual staffing, which together with a sharp post-2024 rise in paid amounts and a large overpayment exposure points to gaps in payroll validation and contract governance; at the same time, employee-level data reveals uneven workload distribution, from healthy, sustainable contributors to high-risk overtime cases like Eric, signaling that alongside financial controls, the organization should invest in proactive staffing forecasts, stronger offboarding and audit protocols and targeted well-being interventions to protect both its finances and its people.
















