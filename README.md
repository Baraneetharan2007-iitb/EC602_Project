# Forecasting Monthly Gold Prices in India: A Box–Jenkins ARIMA Analysis

Group G project for EC602. It applies the **Box–Jenkins univariate methodology** to the monthly average price of
standard gold in Mumbai (₹ per 10 grams) from **January 2000 to December 2020**. The notebook selects an ARIMA model,
evaluates its forecasts out of sample on 2020, and forecasts 2021.

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/USERNAME/REPO/blob/main/EC602_cleaned.ipynb)

---

## Group members (Group G)

| Name | Roll No. |
|---|---|
| Baraneetharan A | 24B1814 |
| Mohd Suzaib Alam | 23B3016 |
| Anusha Kose | 22B4504 |
| Abhishek Kumar | 24B3310 |
| Uday Kumar | 24B2729 |
| V. Karunesh | 24B1828 |

---

## Objective

- Identify, estimate and validate a univariate ARIMA model for monthly gold prices using the Box–Jenkins approach.
- Evaluate the model's forecasting accuracy on data it was not estimated on.
- Produce forecasts with 95% probability limits beyond the sample, and interpret the model economically.

## Data

| | |
|---|---|
| **Series** | Monthly average price of standard gold, Mumbai (₹ per 10 grams) |
| **Source** | Reserve Bank of India, Database on Indian Economy (DBIE), *Gold and Silver Prices* |
| **Link** | https://dbie.rbihub.in/prices/gold-and-silver-prices (older portal: https://data.rbi.org.in) |
| **Period used** | January 2000 – December 2020 (252 monthly observations) |

The notebook reads the DBIE export directly. It accepts **either** DBIE layout, as Excel or CSV:

- a `Month/Year` date column followed by the gold price, **or**
- the DBIE report view, with financial-year blocks (`2025-26`), month rows `APR … MAR` and `Average` rows.

It keeps only the assigned 2000–2020 window and saves it as `gold_2000_2020.csv`.

### Sample split

| Sample | Period | Months | Purpose |
|---|---|---|---|
| Training | Jan 2000 – Dec 2019 | 240 | Identification, estimation, diagnostic checking |
| Test | Jan 2020 – Dec 2020 | 12 | Out-of-sample forecast evaluation |
| Full | Jan 2000 – Dec 2020 | 252 | Re-estimation of the chosen model and forecast for 2021 |

---

## Methodology

Each step follows Box, Jenkins, Reinsel & Ljung (2016). The section numbers below refer to that book.

| Stage | What is done | Book reference |
|---|---|---|
| **Transformation** | Log of price. Its first difference is the monthly log return | §4.1.3, §10.2.4 |
| **Identification** | ADF unit-root test on the log level and on its first difference (d = 1); ACF/PACF of log returns | §10.1, §6.2 |
| **Drift** | Test whether a constant θ₀ is needed in the differenced model | §4.1.3 |
| **Estimation** | Maximum-likelihood fit of ARIMA(0,1,0), (1,1,0), (0,1,1), (1,1,1), (2,1,1), all with drift; comparison by AIC/BIC | Ch. 7, §6.2.4 |
| **Diagnostic checking** | Ljung–Box portmanteau test; residual plots; overfitting check; parameter-redundancy check | §8.1.2, §8.2.2, §7.3.5 |
| **Model selection** | Lowest BIC among models whose residuals pass the Ljung–Box test | §6.2.4 |
| **Forecast evaluation** | 1–12-month forecasts for 2020 with 95% limits; MAE, RMSE, MAPE, interval coverage; rolling one-step forecasts | §5.2.2, §5.2.3 |
| **Final forecast** | Refit on 2000–2020 and forecast Jan–Dec 2021 with 95% limits | Ch. 5 |

### A note on the Ljung–Box test

For a model with d = 1, `statsmodels` sets the first residual equal to the first observation itself (≈ ln 4509 ≈ 8.4).
This one outlier swamps the Ljung–Box statistic and makes every p-value 1.0 whatever the data. The notebook drops the
first *d* residuals before testing and shows the difference in a short demonstration cell.

---

## Results

All numbers used in the report are collected in **Section 6 (Summary of results)** of the notebook. They include:

- ADF statistics and p-values (log level and first difference)
- the AIC/BIC table and the selected model with its estimated drift and volatility
- Ljung–Box p-values for the selected model
- 2020 forecast accuracy (MAE, RMSE, MAPE, 95% interval coverage) for static and rolling forecasts
- the 2021 forecast path with 95% limits

See the project report for the full discussion.

---

## Repository structure

```
.
├── EC602_cleaned.ipynb   # full analysis (the command file)
├── data.xlsx             # raw DBIE export (Excel or CSV)
├── gold_2000_2020.csv    # assigned sample, written by the notebook
├── requirements.txt      # Python dependencies
└── README.md
```

---

## How to run

### Option 1: Google Colab (no setup)
1. Click the **Open in Colab** badge above, or upload `EC602_cleaned.ipynb` at https://colab.research.google.com.
2. Upload `data.xlsx` to the Colab session (use the folder icon in the left sidebar).
3. **Runtime → Run all.**

### Option 2: Locally
```bash
git clone https://github.com/USERNAME/REPO.git
cd REPO
python -m venv .venv && source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook EC602_cleaned.ipynb
```
Then **Kernel → Restart & Run All**.

If the DBIE file has a different name or is a CSV, change the `FILE = "data.xlsx"` line in the data cell.

> **Anaconda users:** if you see *"A module that was compiled using NumPy 1.x cannot be run in NumPy 2.x"*, your base
> environment has mixed package versions. Create a clean environment:
> ```bash
> conda create -n econ python=3.12 numpy pandas scipy statsmodels scikit-learn matplotlib openpyxl ipykernel -y
> conda activate econ
> python -m ipykernel install --user --name econ --display-name "Python (econ)"
> ```
> and select the **Python (econ)** kernel in Jupyter.

---

## References

1. Box, G. E. P., Jenkins, G. M., Reinsel, G. C., & Ljung, G. M. (2016). *Time Series Analysis: Forecasting and Control* (5th ed.). Wiley.
2. Reserve Bank of India. *Database on Indian Economy (DBIE): Gold and Silver Prices.* https://dbie.rbihub.in/prices/gold-and-silver-prices
3. Seabold, S., & Perktold, J. (2010). statsmodels: Econometric and statistical modeling with Python. *Proceedings of the 9th Python in Science Conference.*
4. AI assistance: Claude (Anthropic) was used to review and restructure the analysis notebook and to draft this README.
