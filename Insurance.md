# Insurance Premium and Payout KPI Dashboard

![Project Banner](https://via.placeholder.com/1200x300?text=Insurance+Premium+and+Payout+KPI+Dashboard+Placeholder)

## Table of Contents

- [Project Overview](#-project-overview)
- [Problem Statement](#-problem-statement)
- [Technical Stack](#-technical-stack)
- [Data Model](#-data-model)
- [Key Features](#-key-features)
- [Dashboard Pages](#-dashboard-pages)
- [Advanced DAX Calculations](#-advanced-dax-calculations)
- [Key Insights](#-key-insights)
- [Installation & Usage](#-installation--usage)

---

## Project Overview

**Client:** True Secure Credit Insurance  
**Domain:** Financial Services / Insurance

This project delivers a unified **Business Intelligence Dashboard** designed to track insurance policy performance, analyze premium collections, forecast maturity payouts, and assess the performance of the sales hierarchy. It serves as a central tool for stakeholders—from Zonal Managers to Sales Agents—to make data-driven decisions regarding policy renewals, customer retention, and revenue forecasting.

## Problem Statement

True Secure Credit Insurance required a solution to consolidate data from disparate sources (Policy, Customer, Agent, Region) to answer critical business questions:

- How are different policy types performing across various customer segments?
- What is the projected financial liability (Maturity Amount) for the next 5, 10, and 15 years?
- Which sales agents and regions are driving the most revenue?
- What is the **Annualized ROI** and **CAGR** for policyholders, ensuring transparency in returns?

## Technical Stack

- **Tool:** Microsoft Power BI (Desktop & Service)
- **ETL:** Power Query (Data cleaning, removing top rows, unpivoting, merging queries)
- **Language:** DAX (Data Analysis Expressions)
- **Security:** Row Level Security (RLS)
- **Visualization:** Native Charts, Custom Bookmarks, Field Parameters

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D7?style=for-the-badge&logo=microsoft&logoColor=white)

---

## Data Model

The data model follows a **Star Schema** architecture optimized for performance and filter propagation.

- **Fact Table:** `Insurance Policy` (Contains transactional data: Policy Numbers, Premiums, Dates, Tenures).
- **Dimension Tables:**
  - `Dim_Customer`: Demographics (Age, Gender, Occupation, State).
  - `Dim_Agent`: Sales Agent details.
  - `Dim_PolicyType`: Policy categories (Endowment, ULIP, Term Life).
  - `Dim_Region`: Hierarchy mapping (Zonal Manager -> Regional Manager -> State).

**Relationship Highlights:**

- One-to-Many relationships from Dimensions to Fact.
- Bi-directional filtering enabled for specific sales hierarchy analysis.

---

## Key Features

### 1. Dynamic Metric Switching (Field Parameters)

Users can dynamically change the metric displayed in charts (e.g., swapping "Total Premium" with "Paid Amount" or "Underwriting Expense") without needing separate visuals.

### 2. Interactive View Toggles (Bookmarks)

Implemented toggle buttons to switch between a detailed **Matrix View** (for granular data) and a **Chart View** (for trend analysis), optimizing canvas space.

### 3. Time Intelligence Bucketing

Custom logic to categorize policies based on maturity timeline:

- _"Matured in next 5 years"_
- _"Matured in 10 years"_
- _"Beyond 20 years"_

### 4. Row Level Security (RLS)

Security hierarchy ensures data privacy:

- **Zonal Managers:** View data for their entire zone.
- **Regional Managers:** View data only for their assigned region.
- **Sales Agents:** View only their specific policy sales using `USERPRINCIPALNAME`.

---

## Dashboard Pages

### 1. Summary Page

A high-level executive view featuring:

- **KPI Cards:** Total Active Policies, Total Annual Premium, Total Premium Amount, and Maturity Amount.
- **Slicers:** Filter by Year, Policy Type, and State.

### 2. Insurance Overview

Deep-dive analysis into product performance:

- **Donut/Pie Charts:** Distribution of policies by Type and Name.
- **Trend Analysis:** Premium growth over time (2015–2025).
- **Geospatial Analysis:** State-wise premium contribution.

### 3. Sales Hierarchy Performance

A hierarchical matrix designed for management to drill down from **Zone** → **Region** → **Agent** → **Policy Holder**, tracking individual contribution to the total revenue.

---

## Advanced DAX Calculations

### Maturity Amount

Calculates the final payout based on policy tenure, premium bands, and variable return rates.

```dax
Maturity Amount =
VAR Premium = [Total Annual Premium]
VAR Tenure = MAX('Insurance Policy'[Tenure])
VAR ReturnRate =
    SWITCH(TRUE(),
        Premium < 40000 && Tenure <= 8, 0.08,
        Premium >= 40000 && Tenure > 10, 0.12,
        0.05 -- Default rate
    )
RETURN
    Premium * Tenure * (1 + ReturnRate)
```

### Annualized ROI

Calculates the geometric progression ratio that provides a constant rate of return over the time period.

```dax
Annualized ROI =
VAR FinalValue = [Maturity Amount]
VAR InvestedAmount = [Total Premium Paid]
VAR Years = MAX('Insurance Policy'[Tenure])
RETURN
    IF(
        InvestedAmount > 0,
        POWER(DIVIDE(FinalValue, InvestedAmount), 1 / Years) - 1,
        0
    )
```

### Total Annual Premium (Normalization)

Standardizes premium amounts based on payment frequency.

```dax
Total Annual Premium =
SUMX(
    'Insurance Policy',
    SWITCH(
        'Insurance Policy'[Payment Frequency],
        "Monthly", 'Insurance Policy'[Premium Amount] * 12,
        "Quarterly", 'Insurance Policy'[Premium Amount] * 4,
        "Half-Yearly", 'Insurance Policy'[Premium Amount] * 2,
        'Insurance Policy'[Premium Amount]
    )
)
```

---

## Key Insights

- **Top Products:** **Endowment policies** are the most popular category, contributing to **34.89%** of the total portfolio.
- **Revenue Peak:** The year **2024** witnessed the highest investment in premiums, driven largely by the "ULIP Growth Plan" (collecting over 707M).
- **Sales Performance:** The hierarchical analysis revealed that specific regions in the **South Zone** outperformed others in retaining high-value customers.
- **Customer Behavior:** There is a strong correlation between **Occupation** and **Policy Type**, with "Academic Librarians" and "Managers" showing a preference for long-term security plans.

---

## Installation & Usage

1.  **Download:** Clone this repository or download the `.pbix` file.
2.  **Open:** Ensure you have **Microsoft Power BI Desktop** installed.
3.  **Data Source:** The project uses a static dataset (Excel/CSV). If prompted, update the Data Source Settings to point to the `Data/` folder included in this repo.
4.  **Interact:**
    - Use the **Bookmarks** on the "Sales Hierarchy" page to toggle views.
    - Test **RLS** by going to `Modeling > View as` and selecting different roles.

---
