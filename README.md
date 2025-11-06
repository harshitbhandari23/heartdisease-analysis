# Heart Disease EDA

Exploratory Data Analysis (EDA) on a heart-disease dataset (`heart.csv`) using pandas, seaborn, and matplotlib. The notebook/script inspects schema, handles basic quality checks, and visualizes distributions and relationships across clinical features.

## Dataset

* **File:** `heart.csv`
* **Expected columns** (typical Kaggle-style heart dataset; adapt as needed):

  * `Age`, `Sex`, `RestingBP`, `Cholesterol`, `MaxHR`, `HeartDisease` (0/1)
  * Categorical: `ChestPainType`, `RestingECG`, `ExerciseAngina`, `ST_Slope`

> Update the list above if your columns differ. Run `df.info()` and `df.nunique()` to verify.

## Environment

* Python ≥ 3.9
* Packages:

  * pandas
  * seaborn
  * matplotlib
  * jupyter (optional, if using notebooks)

Install:

```bash
pip install pandas seaborn matplotlib jupyter
```

## How to run

### Option A: Jupyter Notebook

1. Launch Jupyter: `jupyter notebook`
2. Open your notebook and ensure `heart.csv` is in the same directory (or set an absolute path).
3. Run cells top-to-bottom.

### Option B: Python script

1. Save your analysis code into a script, e.g., `heart_eda.py`.
2. Run: `python heart_eda.py`

## What the analysis does

1. **Load & peek**

   * `df.head()`, `df.info()`, `df.isnull().sum()`, `df.nunique()`
2. **Numeric distributions**

   * Age, RestingBP, Cholesterol, MaxHR
3. **Categorical breakdowns** (count plots)

   * Sex totals; Sex × ChestPainType; Sex × RestingECG; Sex × ExerciseAngina; Sex × ST_Slope; Sex × HeartDisease
4. **Pie charts**

   * ChestPainType, ST_Slope, RestingECG composition
5. **Violin plots vs. HeartDisease**

   * Age, RestingBP, and other features
6. **Correlation heatmap**

   * `df.corr(numeric_only=True)` visualized
7. **Joint plots & pair plot**

   * Selected feature pairs; full `pairplot` for quick EDA

## Recommended code updates (to avoid common errors)

Seaborn’s API has changed in recent versions. The following adjustments make the code forward-compatible and fix value-counts column naming:

### Imports

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
```

### Load

```python
df = pd.read_csv("heart.csv")
```

### Distributions (replace deprecated `distplot`)

```python
plt.figure(figsize=(10,10))
plt.subplot(2,2,1)
sns.histplot(data=df, x="Age", kde=True)
plt.subplot(2,2,2)
sns.histplot(data=df, x="RestingBP", kde=True)
plt.subplot(2,2,3)
sns.histplot(data=df, x="Cholesterol", kde=True)
plt.subplot(2,2,4)
sns.histplot(data=df, x="MaxHR", kde=True)
plt.tight_layout()
```

### Categorical count plots

```python
sns.countplot(data=df, x="Sex")
sns.countplot(data=df, x="Sex", hue="ChestPainType")
sns.countplot(data=df, x="Sex", hue="RestingECG")
sns.countplot(data=df, x="Sex", hue="ExerciseAngina")
sns.countplot(data=df, x="Sex", hue="ST_Slope")
sns.countplot(data=df, x="Sex", hue="HeartDisease")
```

### Pie charts (fix `value_counts` usage)

```python
# Chest pain
a = df["ChestPainType"].value_counts().rename_axis("ChestPainType").reset_index(name="count")
plt.figure(figsize=(10,5))
plt.title("Chest Pain Type")
plt.pie(a["count"], labels=a["ChestPainType"], autopct="%0.1f%%", wedgeprops={"edgecolor":"black"})

# ST_Slope
a = df["ST_Slope"].value_counts().rename_axis("ST_Slope").reset_index(name="count")
plt.figure(figsize=(10,5))
plt.title("ST_Slope")
plt.pie(a["count"], labels=a["ST_Slope"], autopct="%0.1f%%", wedgeprops={"edgecolor":"black"})

# RestingECG
a = df["RestingECG"].value_counts().rename_axis("RestingECG").reset_index(name="count")
plt.figure(figsize=(10,5))
plt.title("RestingECG")
plt.pie(a["count"], labels=a["RestingECG"], autopct="%0.1f%%", wedgeprops={"edgecolor":"black"})
```

### Violin plots

* Violin plots expect a numeric `y` (or `x`). Use numeric features against `HeartDisease` or convert categoricals to numeric if needed.

```python
sns.violinplot(data=df, x="HeartDisease", y="Age")
sns.violinplot(data=df, x="HeartDisease", y="RestingBP")
```

Avoid violin plots where `y` is categorical text (e.g., `RestingECG`, `ExerciseAngina`, `ST_Slope`). Use `countplot` or `boxplot` after encoding.

### Correlation heatmap

```python
corr = df.corr(numeric_only=True)
sns.heatmap(corr, annot=True, cmap="viridis")
```

### Joint plots

```python
sns.jointplot(data=df, x="Age", y="Cholesterol", kind="reg")
sns.jointplot(data=df, x="HeartDisease", y="MaxHR", kind="reg")
sns.jointplot(data=df, x="Age", y="MaxHR", kind="reg")
```

Avoid `x="Age", y="Age"` since it’s redundant.

### Pair plot (can be slow)

```python
sns.pairplot(df.select_dtypes(include=["number"]))
```

## Saving figures

Add `plt.tight_layout()` and optionally save to a `figures/` directory:

```python
import os
os.makedirs("figures", exist_ok=True)
plt.savefig("figures/age_dist.png", dpi=200)
```

## Tips

* Ensure categorical columns are `category` dtype for clearer plots and faster operations.
* Check class balance of `HeartDisease` with `df["HeartDisease"].value_counts(normalize=True)`.
* Consider outlier handling (e.g., extreme `Cholesterol`) before modeling.

## Troubleshooting

* **`AttributeError: distplot`**: Replace with `sns.histplot(..., kde=True)`.
* **`KeyError: 'count'` after `value_counts()`**: Use `reset_index(name='count')` as shown above.
* **Overlapping labels**: Call `plt.xticks(rotation=45)` or `plt.tight_layout()`.
* **Slow `pairplot`**: Sample rows or restrict to numeric columns.

## Repository structure (suggested)

```
.
├── heart.csv
├── heart_eda.py        # optional consolidated script
├── notebooks/          # optional .ipynb files
├── figures/            # auto-saved charts
└── README.md
```

## License

Use any OSI-approved license appropriate for your project (MIT, Apache-2.0, etc.).

## Attribution

If your dataset comes from Kaggle or UCI, link the source in this section.
