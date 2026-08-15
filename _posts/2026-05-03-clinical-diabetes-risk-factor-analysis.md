---
title: "Clinical Diabetes Risk Factor Analysis"
date: 2026-05-03 12:00:00 +0900
categories: [Data Analysis, Python]
tags: [Python, Pandas, Data Cleaning, Health Analytics]
---

In this project, I utilized Python and the Pandas library to conduct a comprehensive analysis of patient health metrics to identify key indicators of Diabetes.

I focused on data cleaning, feature engineering based on clinical thresholds (HbA1c), and demographic stratification to pinpoint high-risk patient segments.

The goal was to transform raw physiological data into actionable medical insights, specifically exploring the correlation between age, gender, and diabetic status.

The File I used is titled "diabetes_unclean.csv" and can be found in my github repository, with the link provided at the bottom of this page.

---

### 1. Environment Setup and Data Exploration

I began by importing the necessary libraries and loading the dataset to inspect its structure and initial statistical distribution.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv('diabetes_unclean.csv')
df.head()
# show 5 rows

df.info()
#Summary of the data read

df.describe()
#data statistics of each column
```

*Code Feedback:*

![Output of df.head()](/assets/img/df_head_initial.png)
![Output of df.info()](/assets/img/df_info_output.png)
![Output of df.describe()](/assets/img/df_describe_initial.png)

---

### 2. Auditing and Handling Missing Values

I performed a multi-step audit for missing values to ensure the integrity of the clinical data before dropping incomplete rows.

```python
df.isna().sum()
#total number of missing values in each column

df[df.isna().any(axis=1)]
# search for any rows with missing cell value.

df = df.dropna()
#dropped rows with empty cells

df.isna().sum()
#Now there isn't any empty cells
```

*Code Feedback:*

![Initial NaNs](/assets/img/isna_initial_check.png)
![Rows with NaNs](/assets/img/nan_rows_found.png)
![Verification of 0 NaNs](/assets/img/isna_verification.png)

---

### 3. Column Normalization and Duplicate Identification

I renamed non-standard columns for clarity and conducted recursive checks for duplicate patient records.

```python
df.rename(columns={'No_Pation': 'Patient_ID'}, inplace=True)
display(df.head())
#changed the column No_Pation to Patient_ID and displayed 5 rows

print(df.duplicated('Patient_ID').sum())
#Search for duplicates

df.drop_duplicates('Patient_ID', inplace=True)
#Drop duplicate patients using their ID
print(df.duplicated('Patient_ID').sum())
#Now there isn't any duplicate patient that appears

print(df.duplicated().sum())
#checking for overall rows that are dups.
```

*Code Feedback:*

![Display of renamed head](/assets/img/renamed_head_display.png)
![Print output of first duplicate check](/assets/img/duplicate_print_1.png)
![Print output after dropping duplicates](/assets/img/duplicate_print_2.png)
![Global duplicate check output](/assets/img/global_dup_check.png)

---

### 4. Final Categorical Correction and Readiness Check

After targeting and correcting a specific gender labeling error, I performed a final statistical summary to confirm the data was ready for visualization.

```python
row_that_is_wrong = df[(df['Gender'] != 'M') & (df['Gender'] != 'F')]
row_that_is_wrong
#find the row that has their gender not labeled as M or F

df.loc[df['Patient_ID']== 4543, 'Gender'] = 'F'
#Find patientID 4543 and replace the Gender of the row to F.

df.describe()
#Seeing how many rows were removed
#Now we are ready
```

*Code Feedback:*

![Targeted row for correction](/assets/img/wrong_gender_row.png)
![Final describe output](/assets/img/final_ready_describe.png)

---

### 5. General Population Gender Distribution

I began the visual phase by analyzing the overall gender balance of the entire study cohort.

```python
sns.set_style('darkgrid')
plt.figure(figsize=(6,4))
plt.pie(df['Gender'].value_counts(), labels=df['Gender'].value_counts().index, autopct='%1.1f%%')
plt.title('Gender Distribution')
plt.show()
#Data holds 56.5% of Males and 43.5% Females
```

*Code Feedback:*

![Overall Gender Pie Chart](/assets/img/overall_gender_chart.png)

---

### 6. Engineering Clinical Risk Status

I mapped raw HbA1c values to medical categories (Normal, Pre-diabetes, Diabetes) and verified the new data structure.

```python
conditions = [
    (df['HbA1c'] < 5.7),
    (df['HbA1c'] >=5.7 ) & (df['HbA1c'] < 6.5),
    (df['HbA1c'] >= 6.5)
]
values = ['Normal', 'Pre-diabetes', 'Diabetes']
#Create a condition to label diabetes status.

df['Diabetes_Status'] = np.select(conditions, values, default='Unknown')
df
#Created a new column Diabetes_Status using the condition we just made.
```

*Code Feedback:*

![DataFrame with status column](/assets/img/df_with_diabetes_status.png)

---

### 7. Targeted Analysis: Diabetic Demographic Breakdown

I isolated the diabetic group to examine the raw counts and gender split within this specific population.

```python
diabetes_by_gender = df[df['Diabetes_Status'] == 'Diabetes']
Gender_Count = diabetes_by_gender['Gender'].value_counts()
print("Overall Diabetes Patients Gender Counts:")
print(Gender_Count)

plt.figure(figsize=(6,4))
plt.pie(Gender_Count, labels= Gender_Count.index, autopct='%1.1f%%')
plt.title('Gender Distribution of All Diabetes Patients')
plt.show()

print(Gender_Count)
```

*Code Feedback:*

![Print output of diabetic counts](/assets/img/diabetic_print_counts.png)
![Pie Chart of diabetic gender distribution](/assets/img/all_diabetes_gender_pie.png)

---

### 8. Visualizing Age as a Dominant Risk Variable

I categorized the diabetic population into age divisions, which immediately revealed a heavy skew toward the Senior demographic.

```python
diabetes_patients = df[df['Diabetes_Status'] == 'Diabetes']
age_division = [
    (diabetes_patients['AGE'] < 35),
    (diabetes_patients['AGE'] >= 35 ) & (diabetes_patients['AGE'] < 50),
    (diabetes_patients['AGE'] >= 50)
]
age_group = ['Youth', 'Middle Age', 'Senior']

plt.figure(figsize=(10,6))
sns.countplot(x=np.select(age_division, age_group, default='Unknown'), data=diabetes_patients)
plt.title('Age Division of Patients')
plt.xlabel('Age Division')
plt.ylabel('Count')
plt.show()
#Chart shows Diabetes is way more frequent for those ages equal or above 50.
```

*Code Feedback:*

![Countplot of Age Groups](/assets/img/age_group_countplot.png)

---

### 9. Senior-Specific Gender Distribution

Finally, I examined the gender split specifically within the Senior group (Age 50+) to compare it against the overall diabetic group.

```python
diabetes_by_gender_Senior = df[(df['Diabetes_Status'] == 'Diabetes') & (df['AGE'] >= 50)]
Gender_Count_Senior = diabetes_by_gender_Senior['Gender'].value_counts()

plt.figure(figsize=(6,4))
plt.pie(Gender_Count_Senior, labels= Gender_Count_Senior.index, autopct='%1.1f%%')
plt.title('Gender Distribution of Diabetes Patients in Senior Age Group')
plt.show()
```

*Code Feedback:*

![Senior Specific Pie Chart](/assets/img/senior_gender_pie_final.png)

---

### 10. Final Population Verification

I concluded with a raw count verification to audit why the Senior pie chart closely mirrored the overall diabetic results!

```python
print(f"Total Diabetic Patients: {len(df[df['Diabetes_Status'] == 'Diabetes'])}")
print(f"Senior Diabetic Patients: {len(diabetes_by_gender_Senior)}")
# Was wondering why the pie chart is so identical to the previous one so had to check
```

*Code Feedback:*

![Final Print Outputs](/assets/img/final_audit_prints.png)

---

## Project Resources

You can find the raw data I used in my repository titled :"diabetes_unclean.csv"

📂 [View Project Files on GitHub](https://github.com/alexsyj/cozy_crumb)