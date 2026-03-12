                                            FDA Medical Device Complaint & Recall Analysis

                                            SPARK! Data Days for Good (DD4G) – Spring 2026

                                                   MuckRock Data Liberation Project

Authors:
Dang Dinh · Carol Kong · Mia Vargas · Parin Shaik · Astika Tyagi

Executive Summary

Project Overview

This project presents findings from a comprehensive analysis of FDA medical device complaint and recall data from 2015–2025, conducted as part of the Boston University SPARK! Data Days for Good (DD4G) program in collaboration with MuckRock's Data Liberation Project.

The analysis investigates patterns in medical device safety reporting using data from the FDA MAUDE (Manufacturer and User Facility Device Experience) database and FDA recall records.

The project analyzed:

45.5 million raw complaint entries

19.2 million unique complaint records after cleaning

The goal of the project was to:

1)understand the relationship between complaints and recalls

2)identify trends in device safety reporting

3)explore whether complaint data can serve as an early warning signal for device recalls

4)outline a conceptual predictive framework for investigative journalism

Data & Methodology

Data Pipeline

The analysis required building a large-scale data pipeline on Boston University's Shared Computing Cluster (SCC) to process tens of millions of complaint records.

The pipeline consisted of the following stages:
```

FDA FOI Complaint Data (46M rows)
        │
        ▼
Deduplication & Text Concatenation
        │
        ▼
19.2M Unique Complaint Records
        │
        ▼
Merge with FDA Device Catalog
        │
        ▼
Recall Dataset Integration
        │
        ▼
Problem Code Enrichment
        │
        ▼
Merged Complaint–Recall Dataset
        │
        ▼
Aggregation by Product Code × Year
        │
        ▼
trend_by_year_and_product.csv
```

Data Sources

The project integrates several FDA datasets.
```
| Dataset                     | Source          | Description                              |
| --------------------------- | --------------- | ---------------------------------------- |
| FOI Complaint Dataset       | FDA MAUDE       | Medical device complaint reports         |
| Device Catalog              | FDA MAUDE       | Device identifiers and metadata          |
| Recall Dataset              | FDA Recall Data | Device recall records                    |
| Problem Codes               | FDA MAUDE       | Structured device issue classifications  |
| Problem Code Classification | FDA             | Mapping of problem codes to descriptions |

```

Time range used:
```
2015 – 2025
```

Key Data Challenges

Several issues required careful data engineering:

Missing Date Fields

The ``` DATE_REPORT ``` column in the FOI dataset was entirely null.

Solution:

1)merged FOI complaint records with the device catalog

2)used the ```DATE_RECEIVED``` column as the complaint timestamp.

Duplicate Complaint Records

Each ```MDR_REPORT_KEY``` could contain multiple complaint narrative entries.

Solution:
```
Concatenate all FOI_TEXT entries
→ produce one record per MDR_REPORT_KEY
```

This reduced:
```
46M complaint entries → 19.2M unique complaints
```

Problem Code Inconsistencies

FDA problem code classifications often deviated from the official data dictionary.

Solution:

mapped problem codes using the Problem Code Classification dataset.

Multiple Complaints and Recalls per Product

A product can receive multiple complaints and can be recalled multiple times.

Solution:
```
Aggregate data by product_code × YEAR
```

This produced the final modeling dataset:
```
trend_by_year_and_product.csv
22,558 rows
```
Research Questions

The analysis focused on five primary questions:

Q1: What is the relationship between FDA device complaints and recalls?

Q2: Which types of medical devices are most commonly complained about and recalled?

Q3: What problems are most frequently reported in device complaints?

Q4: What trends exist in complaint and recall data over time?

Q5: Can early warning signals predict device recalls?

Exploratory Data Analysis

Complaint Trends Over Time

Complaint volume increased substantially over the study period:
```
2015: 857,782 complaints
2025: 2,844,272 complaints
```

This represents more than a 3× increase in complaint reporting.

A major spike occurred in 2022, largely driven by complaints related to infusion pumps.

Recall Trends

Analysis of recall data revealed:

1)Class II moderate-risk recalls represent approximately 75% of all recalls.

2)Recalls declined during the COVID-19 pandemic.

3)Recall activity increased again after 2022.

Device Categories

Devices related to diabetes management appeared frequently in complaint data.

Common devices included:

insulin pumps

blood glucose monitors

Problem Code Analysis

The most frequently reported device issues include:

1)device failures

2)algorithm errors

3)alarm failures

4)mechanical defects

Most confirmed recall causes were related to:
```
device design issues
manufacturing defects
```
Relationship Between Complaints and Recalls

Correlation analysis shows that the relationship between complaints and recalls is weak.
```
Correlation coefficient: r = 0.088
```

This suggests that:

FDA recalls are not strongly driven by complaint volume

recalls are often initiated through manufacturer reporting or FDA inspection processes

Recall Timing Analysis

Timeline analysis revealed an important insight:
```
77.1% of device recalls were initiated BEFORE the first complaint was filed
```

This indicates that recalls are frequently proactive rather than reactive.

Example Case Study: ALARIS Infusion Pump

The ALARIS infusion pump provides a clear example of how complaint spikes can signal safety issues.
```
Complaint volume increased 10.8× above baseline in 2020
```

This occurred two years before complaints peaked in 2022, reaching:
```
874,840 complaints
```

This example illustrates how complaint spikes can provide investigative leads, even when overall correlations are weak.

Predictive Modeling Framework

Because of the limited project timeline and data complexity, machine learning was treated as a conceptual research and development exercise.

The goal was to outline how a predictive system could work.

Conceptual Modeling Pipeline
```
Complaint Data
        │
        ▼
Aggregation by Product × Year
        │
        ▼
Feature Construction
• complaint volume
• complaint growth
• recall history
        │
        ▼
Predictive Model
(Logistic Regression)
        │
        ▼
Recall Risk Score
        │
        ▼
Journalistic Investigation
```
Toy Machine Learning Model

A logistic regression model was implemented as a proof-of-concept.

Prediction task:
```

Complaints in year t
→ Recall occurrence in year t+1
```

Model performance:

Metric	Value
Accuracy	~0.77
ROC AUC	~0.71

However, the results reinforce the key finding:
```
Complaint volume alone is not a reliable predictor of recalls.
```
Key Findings

The analysis produced several important insights regarding device complaints and recall behavior:

- **Relationship between complaints and recalls:**  

  There is no strong statistical correlation between complaint volume and recall events. However, complaint data can still be used as a binary classification signal within exploratory predictive models.

- **Trends between complaints and recalls:**  

  Complaint volume increased consistently over time, while recall activity experienced a noticeable increase between **2022–2024**.

- **Most commonly complained products:**  

  Devices related to **diabetes management**, including insulin pumps and glucose monitoring systems, appear most frequently in complaint reports.

- Most common reported problems:

  The most frequent issue categories involve **sterility problems and packaging defects**.

- Early warning signals:

  Complaint data contains significant noise, making it difficult to identify reliable early warning indicators for recalls using complaint volume alone.

Recommendations

Based on the findings, we recommend the following approach for investigative journalism:

Deploy Complaint Spike Detection

Rather than predicting recalls directly, journalists should monitor unusual spikes in complaint volume.

Build Automated Monitoring Tools

MuckRock could develop a monitoring system that:
```
detects complaint spikes
flags high-risk devices
alerts journalists for investigation
```
Future Development

Future improvements could include:

1)NLP analysis of complaint narratives

2)severity scoring for complaint reports

3)manufacturer compliance history

4)device-level modeling beyond product codes

5)integration with additional FDA datasets

Repository Structure
```
├── data
│   ├── raw
│   │   ├── foi.zip
│   │   ├── device.zip
│   │   ├── deviceproblemcodes.zip
│   │   └── device-recall.json
│
│   ├── processed
│   │   ├── complaints_cleaned.csv
│   │   ├── recalls_cleaned.csv
│   │   └── trend_by_year_and_product.csv
│
├── notebooks
│   ├── data_cleaning.ipynb
│   ├── exploratory_analysis.ipynb
│   └── toy_model.ipynb
│
├── outputs
│   ├── visualizations
│   └── next_year_recall_predictions.csv
│
└── README.md
```
Tools Used

Python

Pandas

Polars

Matplotlib

Seaborn

Scikit-learn

Jupyter Notebook

Boston University SCC (HPC cluster)

Acknowledgements

This project was conducted as part of:
```
Boston University SPARK!
MassMutual Data Days for Good (DD4G)
```
In collaboration with:
```

MuckRock – Data Liberation Project
```

Client:
```
Dillon Bergin
Senior Investigative & Data Journalist
```
Authors

Dang Dinh

Carol Kong

Mia Vargas

Parin Shaik

Astika Tyagi

Boston University
Spring 2026
