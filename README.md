# Experiment 4: Data Wrangling and Data Visualization

## Overview

This repository contains the Jupyter Notebook solution for **PA#4: Data
Wrangling and Data Visualization**. The activity uses Python's `pandas`
and `matplotlib` libraries to work with the **ECE Board Exam 2 dataset
(`board2.csv`)**.

The notebook focuses on:

-   Loading tabular data using Pandas.
-   Creating filtered DataFrames using multiple categorical conditions.
-   Selecting specific columns from filtered data.
-   Displaying subsets based on an `average` score.
-   Computing categorical mean averages for **Track**, **Gender**, and
    **Hometown**.
-   Visualizing the category means using three side-by-side bar charts.

------------------------------------------------------------------------

## Intended Learning Outcomes (ILOs)

After completing the activity, the student should be able to:

-   Manipulate tabular data using Python's `pandas` library.
-   Apply multiple conditions when filtering a DataFrame.
-   Select specific features from a dataset.
-   Perform group-based aggregation using `groupby()`.
-   Calculate and compare mean values across categorical groups.
-   Present categorical comparisons using appropriately labeled bar
    charts.

------------------------------------------------------------------------

## Dataset Schema

The analysis uses the **ECE Board Exam 2 dataset (`board2.csv`)**, which
contains student information and board examination scores.

  -----------------------------------------------------------------------
  Column Name             Type                    Description
  ----------------------- ----------------------- -----------------------
  `Name`                  Categorical             Student identifier code

  `Gender`                Categorical             Student gender

  `Track`                 Categorical             ECE track
                                                  specialization

  `Hometown`              Categorical             Student regional origin

  `Math`                  Numerical               Board exam score in
                                                  Mathematics

  `Electronics`           Numerical               Board exam score in
                                                  Electronics Engineering

  `GEAS`                  Numerical               Board exam score in
                                                  General Engineering and
                                                  Applied Sciences

  `Communication`         Numerical               Board exam score in
                                                  Communication
                                                  Engineering

  `average` / `Average`   Numerical               Overall board
                                                  examination score
                                                  average used by the
                                                  notebook
  -----------------------------------------------------------------------

### Dataset Categories

-   **Gender:** Male, Female
-   **Track:** Communication, Microelectronics, Instrumentation
-   **Hometown:** Luzon, Visayas, Mindanao

------------------------------------------------------------------------

## ⚙️ Solutions & Implementation Summary

  -----------------------------------------------------------------------------------
  Problem            Main Operation               Selected Features Primary Output
                                                  / Variables       
  ------------------ ---------------------------- ----------------- -----------------
  **Part A: Visayas  Filter                       `Name`, `Gender`, `VisComm`
  Communication      `Hometown == "Visayas"` and  `Math`,           DataFrame
  DataFrame**        `Track == "Communication"`   `Electronics`,    
                                                  `average`         

  **Part B: Visayas  Filter                       `Name`, `Track`,  `VisFemale`
  Female DataFrame** `Hometown == "Visayas"` and  `GEAS`,           DataFrame +
                     `Gender == "Female"`         `Electronics`,    `average >= 60`
                                                  `average`         subset

  **Part C:          Group by `Track`, `Gender`,  Categorical       3 summary
  Category-Average   and `Hometown`               groups +          tables + 1×3
  Visualization**                                 `Average`         bar-chart figure
  -----------------------------------------------------------------------------------

------------------------------------------------------------------------

# Problem Specifications & Code Solutions

## Setup & Data Loading

The notebook loads the CSV dataset using Pandas:

``` python
import pandas as pd

df = pd.read_csv('board2.csv')

df
```

The notebook uses:

-   **pandas** -- data manipulation and DataFrame operations.
-   **matplotlib** -- data visualization and bar charts.

------------------------------------------------------------------------

## Part A: Visayas Communication DataFrame

### Requirement

Create a DataFrame containing students whose:

-   `Hometown` is **Visayas**
-   `Track` is **Communication**

The notebook selects:

`Name`, `Gender`, `Math`, `Electronics`, and `average`.

### Code

``` python
VisComm = df[(df["Hometown"] == "Visayas") & (df["Track"] == "Communication")][
    ["Name", "Gender", "Math", "Electronics", "average"]]

print("VisComm DataFrame:")
print(VisComm)
```

### Output

The notebook produces the following records:

  Name   Gender     Math   Electronics   average
  ------ -------- ------ ------------- ---------
  S11    Female       48            56     54.75
  S12    Male         89            67     76.00
  S18    Male         81            40     63.50
  S22    Female       64            39     62.50
  S28    Male         85            53     67.75

------------------------------------------------------------------------

## Part B: Visayas Female DataFrame

### Requirement

Create a DataFrame containing students whose:

-   `Hometown` is **Visayas**
-   `Gender` is **Female**

The notebook selects:

`Name`, `Track`, `GEAS`, `Electronics`, and `average`.

It then displays a non-destructive subset where `average >= 60`.

### Code

``` python
VisFemale = df[(df['Hometown']== 'Visayas')& (df['Gender'] == 'Female')][
['Name','Track', 'GEAS', 'Electronics','average']]

print(VisFemale )
print()
print ("VisFemale with average >= 60")
display( VisFemale[VisFemale['average'] >= 60])
```

### Full `VisFemale` Output

  Name   Track                GEAS   Electronics   average
  ------ ------------------ ------ ------------- ---------
  S6     Microelectronics       86            45     75.50
  S11    Communication          48            56     54.75
  S21    Microelectronics       68            51     68.50
  S22    Communication          89            39     62.50
  S24    Microelectronics       60            45     57.75
  S26    Instrumentation        83            47     65.75

### `average >= 60` Output

  Name   Track                GEAS   Electronics   average
  ------ ------------------ ------ ------------- ---------
  S6     Microelectronics       86            45     75.50
  S21    Microelectronics       68            51     68.50
  S22    Communication          89            39     62.50
  S26    Instrumentation        83            47     65.75

------------------------------------------------------------------------

# Part C: Category-Average Visualization

### Requirement

Compute the mean `Average` score for each category of:

-   **Track**
-   **Gender**
-   **Hometown**

Then display the three summary tables and create one figure containing
three side-by-side bar charts.

### Code

``` python
import matplotlib.pyplot as plt

df = pd.read_csv('board2.csv')

if "Average" not in df.columns:
    df["Average"] = df[
        ["Math", "GEAS", "Electronics", "Communication"]
    ].mean(axis=1)

track_mean = df.groupby("Track", as_index=False)["Average"].mean()
gender_mean = df.groupby("Gender", as_index=False)["Average"].mean()
hometown_mean = df.groupby("Hometown", as_index=False)["Average"].mean()

print("Mean Average by Track:")
display(track_mean)

print("\nMean Average by Gender:")
display(gender_mean)

print("\nMean Average by Hometown:")
display(hometown_mean)

fig, axes = plt.subplots(1, 3, figsize=(15, 5))

axes[0].bar(track_mean["Track"], track_mean["Average"])
axes[0].set_title("Mean Average by Track")
axes[0].set_xlabel("Track")
axes[0].set_ylabel("Mean Average")

axes[1].bar(gender_mean["Gender"], gender_mean["Average"])
axes[1].set_title("Mean Average by Gender")
axes[1].set_xlabel("Gender")

axes[2].bar(hometown_mean["Hometown"], hometown_mean["Average"])
axes[2].set_title("Mean Average by Hometown")
axes[2].set_xlabel("Hometown")

plt.tight_layout()
plt.show()

print(
    "1. The Communication track had the highest sample mean Average score (67.98)."
)
print(
    "2. Male students had the highest sample mean Average score (67.18)."
)
print(
    "3. Students from Luzon had the highest sample mean Average score (68.08)."
)
```

------------------------------------------------------------------------

## Summary Tables Output

### Mean Average by Track

  Track                Average
  ------------------ ---------
  Communication         67.975
  Instrumentation       65.225
  Microelectronics      67.500

### Mean Average by Gender

  Gender       Average
  -------- -----------
  Female     66.616667
  Male       67.183333

### Mean Average by Hometown

  Hometown       Average
  ---------- -----------
  Luzon        68.083333
  Mindanao     66.678571
  Visayas      65.750000

------------------------------------------------------------------------

## Observed Category Means

Based on the notebook's computed results:

-   **Track:** Communication has the highest sample mean Average at
    **67.98**.
-   **Gender:** Male has the highest sample mean Average at **67.18**.
-   **Hometown:** Luzon has the highest sample mean Average at
    **68.08**.

These statements describe the observed group means in the dataset. They
do **not** imply that membership in a category causes a higher
board-exam score.

------------------------------------------------------------------------

## Prerequisites & Requirements

Ensure Python 3.8+ is installed along with the required libraries.

``` bash
pip install pandas matplotlib
```

### Libraries

-   `pandas` -- data manipulation and DataFrame filtering.
-   `matplotlib` -- data visualization and bar-chart rendering.

------------------------------------------------------------------------

## How to Run

1.  Place `board2.csv` in the same directory as the notebook.
2.  Open `PA_4_Macagba,Karl.ipynb` using Jupyter Notebook or VS Code.
3.  Make sure the required libraries are installed.
4.  Run the notebook cells sequentially from top to bottom.

For Jupyter Notebook:

``` bash
jupyter notebook
```

------------------------------------------------------------------------

## Key Findings & Empirical Analysis

-   **Track:** The Communication group recorded the highest sample mean
    Average (**67.98**), followed by Microelectronics (**67.50**) and
    Instrumentation (**65.23**).
-   **Gender:** The Male group had a sample mean Average of **67.18**,
    while the Female group had **66.62**.
-   **Hometown:** The Luzon group recorded the highest sample mean
    Average (**68.08**), followed by Mindanao (**66.68**) and Visayas
    (**65.75**).
-   The results are descriptive of the dataset analyzed in the notebook.
    Differences in group means should not, by themselves, be interpreted
    as evidence that a category causes differences in board-exam
    performance.

------------------------------------------------------------------------

## Student Information

**Name:** Macagba, Karl\
**Section:** 2ECE-A\
**Activity:** PA#4\
**Topic:** Data Wrangling and Data Visualization

------------------------------------------------------------------------

## Files

``` text
.
├── PA_4_Macagba,Karl.ipynb
├── board2.csv
└── README.md
```
