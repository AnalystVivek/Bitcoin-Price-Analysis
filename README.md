# Bitcoin Price Analysis 📊💰

This project presents an exploratory data analysis (EDA) of Bitcoin price data from April 2013 to July 2017. The goal is to uncover trends, visualize volatility, and understand market behavior using Python data science tools. The analysis demonstrates skills in data cleaning, statistical summarization, time series analysis, and interactive visualizations.

---

## Dataset Overview 📅

The dataset contains daily Bitcoin historical prices with these columns:

- `Date`: Trading day 
- `Open`, `High`, `Low`, `Close`: Price indicators in USD
- `Volume`: Trading volume (string format, requires cleaning)
- `Market Cap`: Market capitalization (string format, requires cleaning)

The data covers 1556 records, spanning from **2013-04-28** to **2017-07-31**.

---

## Project Steps and Key Insights 🚀
<br><br>
### 1. Data Loading and Cleaning 🧹
<br><br>
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import plotly.graph_objs as go
import plotly.express as px
from plotly.offline import init_notebook_mode, iplot

init_notebook_mode(connected=True) # For offline use in Jupyter
```
Load dataset
```
df = pd.read_csv('bitcoin_dataset.csv')
```
Convert Date to datetime
```
df['Date'] = pd.to_datetime(df['Date'])
```
Sort dataset by Date
```
df.sort_values(by='Date', ascending=True, inplace=True)
```
Check for missing values and duplicates
```
print(df.isnull().sum())
print(df.duplicated().sum())
```

- No missing values or duplicates confirmed.  
- Dates converted for time series analysis.

---
<br><br>
### 2. Statistical Summary of Price Columns 📈
<br><br>
Summary statistics for Open, High, Low, Close columns
```
print(df[['Open', 'High', 'Low', 'Close']].describe().T)
```
<img width="500" height="221" alt="Screenshot 2025-08-23 033140" src="https://github.com/user-attachments/assets/1b306556-dba4-4f72-85d7-b5dbc4efd735" />

**Insight:** 
- Price range highlights high volatility: from around 68 USD to nearly 3,000 USD, indicating the high volatility Bitcoin experienced.
- Standard deviations confirm significant fluctuations, typical of cryptocurrency markets.

---
<br><br>

### 3. Visualizing Price Trends over time 📊
<br><br>
```
plt.figure(figsize=(20, 12))

for idx, col in enumerate(['Open', 'High', 'Low', 'Close'], start=1):
    plt.subplot(2, 2, idx)
    plt.plot(df['Date'], df[col])
    plt.title(f"{col} Price - Over Time")
    plt.show()
```
**Insight:** 
- The line plots illustrate a clear upward trend in Bitcoin prices over the years with noticeable volatility spikes—highlighting phases of rapid growth and declines.

<img width="1200" height="790" alt="overtime_open_close_high_low" src="https://github.com/user-attachments/assets/20602df8-6ae5-4c19-9162-a0607f5c5b6f" />


---
<br><br>
### 4. Candlestick Chart (First 100 Records) 💹
<br><br>
Limiting data to first 100 for clarity
```
data_sample = df.head(100)
```

Create candlestick trace
```
trace = go.Candlestick(
             x=data_sample['Date'],
             open=data_sample['Open'],
             high=data_sample['High'],
             low=data_sample['Low'],
             close=data_sample['Close']
)

fig = go.Figure(data=[trace])

fig.update_layout(
        title="Bitcoin Price Analysis",
        yaxis_title="Price (in USD)",
        xaxis_title="Date",
        xaxis_rangeslider_visible=False # Hide range slider
)

iplot(fig)
```
**Insight:** 
- The candlestick chart provides a detailed daily price movement visualization, enabling insights into market sentiment and volatility within short timespans.
<img width="1122" height="450" alt="candlestick" src="https://github.com/user-attachments/assets/d81b1438-0890-478c-8d5f-fbc4051a92db" />


---
<br><br>
### 5. Closing Price Over Time (Line Plot)
<br><br>
```
df.set_index('Date', inplace=True) # Set Date as index for time series
```

Basic closing price plot
```
df['Close'].plot(title="Closing Price Over Time")
plt.show()
```

Interactive Plotly line plot
```
fig = px.line(df, y='Close', title='Closing Price Over Time')
fig.show()
```
<img width="1122" height="450" alt="closingprice" src="https://github.com/user-attachments/assets/b492fada-5d22-45a1-8aed-54c30b8cc928" />

**Insight:**  
- The steady upward slope reflects Bitcoin's long-term growth, with visible fluctuations and corrections reinforcing the volatile nature of the asset.

---
<br><br>
### 6. Side-by-side linear and Logarithmic Scale Visualization 📉📈
<br><br>
```
plt.figure(figsize=(20, 8))

plt.subplot(1, 2, 1)
plt.plot(df.index, df['Close'])
plt.title('No Scale')

plt.subplot(1, 2, 2)
plt.plot(df.index, np.log1p(df['Close']))
plt.title('Log Scale')

plt.show()
```

<img width="1614" height="682" alt="closingprice_log_scale" src="https://github.com/user-attachments/assets/f6a97c1a-a0e6-4a56-a17b-1fc6ee84a9f9" />
**Insight:**  
- The logarithmic scale plot accentuates relative percentage changes, making exponential growth and minor variations more interpretable compared to the raw linear scale.

---
<br><br>
### 7. Resampling to Analyze Trends ⏳
<br><br>
- Yearly mean closing price
```
yearly_mean = df['Close'].resample('YE').mean()

yearly_mean.plot(title='Yearly Mean Closing Price', figsize=(8, 3))

plt.show()
```
<img width="1122" height="450" alt="yearly_meanclosing" src="https://github.com/user-attachments/assets/a25de1bc-08e8-4bb0-96cf-8618c5adea0e" />
<br><br>

- Quarterly mean closing price
```
quarterly_mean = df['Close'].resample('QE').mean()

fig_q = px.line(quarterly_mean, y='Close', title='Quarterly Mean Closing Price', markers=True)

fig_q.update_traces(
          mode='lines+markers+text',
          text=quarterly_mean.round(2),
          textposition='top center',
          texttemplate='%{text}',
          textfont=dict(size=10)
)

fig_q.show()
```
<img width="1122" height="450" alt="quaterly_meanclosing" src="https://github.com/user-attachments/assets/76a00f8e-5303-47b7-b120-0626756e861b" />
<br><br>

- Monthly mean closing price
```
monthly_mean = df['Close'].resample('ME').mean()

fig_m = px.line(monthly_mean, y='Close', title='Monthly Mean Closing Price', markers=True)

fig_m.update_traces(
        mode='lines+markers+text',
        text=monthly_mean.round(2),
        textposition='top center',
        texttemplate='%{text}',
        textfont=dict(size=4)
)

fig_m.show()
```
<img width="2147" height="821" alt="monthly_meanclosing" src="https://github.com/user-attachments/assets/9381d079-0504-4655-b3bb-51b9e370906b" />

**Insight:**  
- Yearly resampling reveals robust growth, particularly a sharp rise in 2017.  
- Quarterly and monthly views expose shorter cycles and volatility patterns, critical for traders analyzing market timing
---
<br><br>
### 8. Daily Percentage Change in Closing Price 📉📈
<br><br>
Calculate daily percentage change
```
df['close_pc'] = df['Close'].pct_change() * 100
```

Interactive plot

```
fig_pct = px.line(df['close_pc'], title='Daily Percentage Change in Closing Price', labels={'value':'Percentage Change'})

fig_pct.update_traces(line=dict(color='green', width=1))

fig_pct.show()
```

<img width="1365" height="525" alt="Daily Percentage Change in Closing Price" src="https://github.com/user-attachments/assets/076f5a07-d510-4028-85b9-85fe79894fce" />

**Insight:**  
- Daily percentage change analysis highlights the volatile nature of Bitcoin. Sharp spikes indicate periods of rapid price moves, important for risk assessment and trading strategy development.
---

## Summary 📝

- Data was preprocessed with robust cleaning and date formatting for time series analysis.  
- Basic price statistics highlighted Bitcoin’s high volatility and substantial price increase from 2013 to 2017.  
- Line plots, candlestick charts, and log scale graphs effectively depicted price trends and volatility.  
- Resampling helped capture broader market cycles at annual, quarterly, and monthly levels.  
- Daily percentage change quantified short-term price fluctuations, a key to understanding market risk.

---

## Conclusion 🔍
- Through this project, I have demonstrated my ability to deliver deep market insights and risk profiles from large-scale financial datasets. The outputs align with professional norms for exploratory analytics, visual storytelling, and actionable intelligence in the financial sector. This skill set can drive data-driven investment decisions, enhance risk management protocols, and support strategic business outcomes in any modern financial organization.

---

## Files Included 📂

- [bitcoin_dataset.csv](https://github.com/AnalystVivek/Bitcoin-Price-Analysis/blob/main/bitcoin_dataset.csv) — Source price data.  
- [bitcoin_analysis.html](https://github.com/AnalystVivek/Bitcoin-Price-Analysis/blob/main/bitcoin_analysis.html) — Full Jupyter Notebook export with code and interactive visualizations.
- [bitcoin_analysis.ipynb](https://github.com/AnalystVivek/Bitcoin-Price-Analysis/blob/main/bitcoin_analysis.ipynb) — Jupyter Notebook Python Code File

---

*This project highlights practical data analysis skills with real-world financial data.* 🌟

---













