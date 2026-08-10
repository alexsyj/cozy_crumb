---
title: "Smartwatch Health Data: From Raw CSV to Clinical Insights"
date: 2026-08-02 12:00:00 +0900
categories: [Data Analysis, Excel]
tags: [Power Query, Analytics, Excel, Health Data]
---

This project demonstrates a full data pipeline within Microsoft Excel—starting from a messy **CSV export** and ending with an **interactive dashboard** that identifies correlations between activity, sleep, and stress.

## 1. Data Preparation & Cleaning

#### Raw Data Preview (CSV)
Before cleaning: Notice the unformatted headers and raw sensor values.

![Screenshot of raw CSV data](/assets/img/unclean_preview.png)

The original file, `unclean_smartwatch_health_data.csv`, contained raw sensor logs with inconsistent formatting and missing values.

> **Cleaning Workflow:**
> * Imported raw CSV into Excel and converted to Table format for structured referencing.
> * Used **Power Query** to standardize column headers and fix data types.
> * Handled missing Heart Rate and Blood Oxygen values to ensure statistical accuracy.

## 2. Feature Engineering

#### Processed Data (XLSX)
After processing: Headers are cleaned, and new calculated columns have been engineered.

![Screenshot of cleaned Excel data](/assets/img/cleaned_data_preview.png)

I moved beyond basic cleaning to create **derived metrics** that provide deeper context into user health:

* **Stress Category:** A qualitative label (Low, Medium, High) based on raw stress scores.
* **Stress per 1k Steps:** A custom efficiency metric to measure the physical impact of movement on stress reduction.

*Resulting file: `clean_data_1.xlsx`*

## 3. Key Visual Analysis

### Sleep Duration vs. Stress Level
This Pivot Chart identifies the "recovery point" where stress levels drop significantly relative to sleep hours.

![Pivot Chart showing Sleep vs Stress](/assets/img/sleep_vs_stress.png)

### Activity Impact on Stress
Analyzing the **Stress per 1k Steps** metric reveals how different activity intensities affect physiological strain.

---

## Project Resources

Download the dataset files used in this pipeline:
* 📄 [Download Raw CSV File](/assets/unclean_smartwatch_health_data.csv)
* 📊 [Download Cleaned Excel Dataset](/assets/clean_data_1.xlsx)

📂 [View Project Files on GitHub](https://github.com/alexsyj)