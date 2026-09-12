# 🧬 Clinical Trial Survival Analysis

## 📌 Project Overview

This project analyzes **patient survival data from a clinical trial** to evaluate the effectiveness of different treatments and identify factors associated with patient survival.

The analysis uses **Survival Analysis**, a statistical approach used to study the time until an event occurs. In this project, the primary event of interest is **patient death**.

The project uses:

* Kaplan–Meier Survival Analysis
* Kaplan–Meier Survival Curves
* Median Survival Time
* Log-Rank Test
* Cox Proportional Hazards Model
* Cox Time-Varying Proportional Hazards Model
* Categorical Variable Encoding
* Data Preprocessing
* Exploratory Data Analysis

---

## 🎯 Objectives

The main objectives of this project are:

1. Understand and preprocess clinical trial data.
2. Analyze patient survival times.
3. Compare survival outcomes between **standard and test treatments**.
4. Visualize survival probabilities using Kaplan–Meier curves.
5. Calculate median survival time for treatment groups.
6. Determine whether treatment groups have statistically different survival distributions.
7. Identify patient characteristics associated with survival using the Cox Proportional Hazards model.
8. Explore time-varying survival analysis.
9. Handle censored observations appropriately.
10. Demonstrate an end-to-end survival analysis workflow.

---

## 🧠 What is Survival Analysis?

Survival analysis is a statistical technique used to analyze **time-to-event data**.

Examples of events include:

* Patient death
* Disease recurrence
* Recovery
* Machine failure
* Customer churn

In this project:

```text
Event = Patient Death
Time = Survival / Observation Time
```

A key characteristic of survival data is **censoring**.

### Censoring

Censoring occurs when the event of interest has not been observed for a patient during the observation period.

The dataset represents observations using the `Y` variable:

```text
Y = 0 → Start of observation / censored observation
Y = 1 → Death / event
```

---

# 📊 Dataset

The project uses the **Veteran lung cancer clinical trial dataset** loaded from the provided CSV source.

The dataset contains patient-level clinical information including treatment, cancer cell type, health score, age, diagnosis time, and prior therapy.

### Dataset Variables

| Column         | Description                                     |
| -------------- | ----------------------------------------------- |
| `ID`           | Patient ID                                      |
| `TIME`         | Time in days                                    |
| `Y`            | Event indicator: 0 = start/censoring, 1 = death |
| `trt`          | Treatment type: `standard` or `test`            |
| `celltype`     | Type of lung cancer                             |
| `karno`        | Karnofsky score representing patient health     |
| `diagtime`     | Months from diagnosis to study entry            |
| `age`          | Patient age in years                            |
| `priortherapy` | Whether the patient received previous therapy   |

Each patient is represented by observations corresponding to the start of observation and the event/censoring information.

---

# 🛠️ Technologies Used

| Technology                          | Purpose                  |
| ----------------------------------- | ------------------------ |
| **Python**                          | Programming and analysis |
| **Pandas**                          | Data manipulation        |
| **NumPy**                           | Numerical operations     |
| **Matplotlib**                      | Data visualization       |
| **Lifelines**                       | Survival analysis        |
| **Jupyter Notebook / Google Colab** | Development environment  |

---

# 📚 Python Libraries

The major libraries used are:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from lifelines import (
    KaplanMeierFitter,
    CoxPHFitter,
    CoxTimeVaryingFitter
)

from lifelines.statistics import logrank_test
```

Install the main survival-analysis library using:

```bash
pip install lifelines
```

---

# 🔄 Project Workflow

```text
Clinical Trial Dataset
        ↓
Data Loading
        ↓
Data Understanding
        ↓
Data Preprocessing
        ↓
Exploratory Analysis
        ↓
Kaplan–Meier Analysis
        ↓
Median Survival Analysis
        ↓
Log-Rank Test
        ↓
Cox Proportional Hazards Model
        ↓
Cox Time-Varying Model
        ↓
Interpretation & Insights
```

---

# 🧹 Data Preprocessing

The project follows a data-preparation workflow that includes:

### 1. Data Understanding

The dataset is inspected using:

```python
df.head()
df.tail()
df.info()
df.shape
df.describe()
```

### 2. Data Cleaning

The project considers:

* Data types
* Unwanted columns
* Duplicate observations
* Missing values
* Outliers
* Zero / near-zero variance
* Categorical variables

### 3. Categorical Encoding

Categorical variables are converted into numerical dummy variables using:

```python
pd.get_dummies()
```

Variables encoded include:

* `trt`
* `celltype`
* `priortherapy`

---

# 📈 Kaplan–Meier Survival Analysis

The **Kaplan–Meier estimator** is used to estimate the probability that a patient survives beyond a particular point in time.

The project generates an overall survival curve and treatment-specific survival curves.

```python
kmf = KaplanMeierFitter()

kmf.fit(
    durations=df_event['TIME'],
    event_observed=df_event['Y']
)

kmf.plot_survival_function()
```

### Treatment Comparison

Separate survival curves are generated for:

```text
Standard Treatment
        vs
Test Treatment
```

This makes it possible to visually compare survival probabilities over time.

---

# ⏱️ Median Survival Time

The project calculates the **median survival time** for each treatment group.

Median survival time represents the point at which the estimated survival probability reaches approximately:

```text
50%
```

The project also handles cases where median survival is not reached during the observed period.

---

# 🧪 Log-Rank Test

The **Log-Rank Test** is used to statistically compare the survival distributions of two treatment groups.

The project compares:

```text
Standard Treatment
        vs
Test Treatment
```

Example:

```python
results = logrank_test(
    group1['TIME'],
    group2['TIME'],
    event_observed_A=group1['Y'],
    event_observed_B=group2['Y']
)
```

The p-value is then interpreted using a significance level of:

```text
α = 0.05
```

### Interpretation

```text
p < 0.05
→ Evidence of a statistically significant difference

p ≥ 0.05
→ No strong evidence of a statistically significant difference
```

---

# 📊 Cox Proportional Hazards Model

The **Cox Proportional Hazards model** is used to investigate how different patient characteristics are associated with the hazard of the event.

The project includes variables such as:

* Age
* Karnofsky score
* Diagnosis time
* Treatment
* Cancer cell type
* Prior therapy

Categorical variables are converted into dummy variables before fitting the model.

```python
cph = CoxPHFitter()

cph.fit(
    cox_df,
    duration_col='TIME',
    event_col='Y'
)

cph.print_summary()
```

The model provides information such as:

* Coefficients
* Hazard ratios
* Statistical significance
* Confidence intervals

---

# 🔬 Cox Time-Varying Model

The project also explores a **Cox Time-Varying Proportional Hazards model**.

The original data is transformed into a start-stop format:

```text
ID
start
stop
event
```

This allows the analysis to represent observations over time.

The model is fitted using:

```python
ctv = CoxTimeVaryingFitter()

ctv.fit(
    df_timevarying,
    id_col='ID',
    start_col='start',
    stop_col='stop',
    event_col='event'
)
```

The resulting coefficients are also visualized to understand the contribution of the model variables.

---

# 📊 Visualizations

The project produces several survival-analysis visualizations.

### Kaplan–Meier Survival Curve

Shows estimated survival probability over time.

### Survival by Treatment

Compares:

```text
Standard Treatment
vs
Test Treatment
```

### Median Survival Comparison

A bar chart is used to compare median survival times between treatment groups.

### Cox Model Coefficients

The Cox model coefficients are plotted to visualize the estimated effects of variables.

---

# 🔍 Key Analysis Questions

The project attempts to answer questions such as:

### Treatment Effect

> Does the test treatment produce different survival outcomes compared with the standard treatment?

### Survival Probability

> How does the probability of survival change over time?

### Median Survival

> What is the estimated median survival time for each treatment group?

### Patient Factors

> Which patient characteristics are associated with survival?

### Statistical Significance

> Is the difference between treatment groups statistically significant?

### Time-Varying Effects

> Can survival risk be analyzed using a start-stop/time-varying framework?

---

# 💡 Key Concepts Demonstrated

This project demonstrates practical understanding of:

* Survival Analysis
* Censored Data
* Time-to-Event Analysis
* Kaplan–Meier Estimator
* Survival Probability
* Median Survival
* Log-Rank Test
* P-values
* Cox Proportional Hazards
* Hazard Ratios
* Cox Time-Varying Models
* Feature Encoding
* Data Preprocessing
* Exploratory Data Analysis
* Statistical Modeling
* Data Visualization

---

# 📁 Project Structure

```text
Clinical-Trial-Survival-Analysis/
│
├── Clinical_Trial_Survival_Analysis.ipynb
│
├── README.md
│
└── data/
    └── veteran.csv
```

> The notebook currently loads the dataset directly from its configured CSV source. If you add `veteran.csv` to the repository, update the notebook's data-loading path accordingly.

---

# ▶️ How to Run the Project

## 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/Clinical-Trial-Survival-Analysis.git
```

```bash
cd Clinical-Trial-Survival-Analysis
```

## 2. Install Dependencies

```bash
pip install pandas numpy matplotlib lifelines jupyter
```

## 3. Launch Jupyter Notebook

```bash
jupyter notebook
```

Open:

```text
Clinical_Trial_Survival_Analysis.ipynb
```

Then run the notebook cells sequentially.

---

# ☁️ Google Colab

The notebook can also be executed using **Google Colab**.

1. Upload `Clinical_Trial_Survival_Analysis.ipynb`.
2. Run the installation cell.
3. Execute the notebook from top to bottom.
4. Review the survival curves and statistical model outputs.

---

# 🚀 Future Improvements

Potential extensions to make this project more advanced:

* Create an interactive **Streamlit survival-analysis dashboard**
* Add patient risk-score prediction
* Add survival probability prediction at specific time points
* Perform proportional-hazards assumption checks
* Add confidence intervals to survival curves
* Compare additional clinical subgroups
* Perform model validation
* Add automated statistical reporting
* Deploy the analysis as a web application
* Create a Power BI dashboard for clinical-trial exploration

---

# 📌 Project Takeaway

This project demonstrates how **survival-analysis techniques can be used to analyze clinical trial data**, compare treatment groups, account for censored observations, and investigate how patient characteristics relate to survival outcomes.

It combines **data preprocessing, statistical testing, visualization, and survival modeling** into an end-to-end data-analysis workflow.

---

# 👨‍💻 Author

**Siddharth Singh**

Aspiring Data Analyst | Data Science Enthusiast

### Skills Demonstrated

`Python` `Pandas` `NumPy` `Statistics` `Matplotlib` `Lifelines` `Survival Analysis` `Data Visualization` `Statistical Modeling`

---

## ⭐ If you found this project useful

Feel free to ⭐ **star the repository** and explore the notebook.
