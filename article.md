# Pipeline Safety Reporting: Moving the Annual Report Deadline from March 15 to June 15 In June 2025, the Pipeline and Hazardous Materials Safety Administration
(PHMSA) announced a proposal to move the annual report filing...

::::::::### Pipeline Safety Reporting: Moving the Annual Report Deadline from March 15 to June 15 

In June 2025, the Pipeline and Hazardous Materials Safety Administration
(PHMSA) announced a proposal to move the annual report filing deadline
for gas pipelines and underground natural gas storage from March 15 to
June 15, aligning gas operators with hazardous liquid pipeline
requirements. This regulatory change aims to reduce compliance costs and
relieve unnecessary operational pressure without compromising safety.

The March 15 annual report deadline has been in place since 1984, a
period when the required filings were far simpler (87 FR 18956). The
original gas distribution report was two pages; the gas transmission
form was just one page. Today, these forms run for up to 22 pages and
require a far more detailed accounting of assets, operations, and
incidents. Over four decades, the reporting task has expanded while the
time to comply has not.

An analysis of seven years of underground natural gas storage report
filings shows a pronounced surge in submissions in mid-March --- just
before the current deadline. 78.1% of all PHMSA annual underground
natural gas reports are filed between March 1 and March 15.

#### Annual Report Filing Timeline (2018--2025)


<figcaption><em>Monthly filings over seven years, showing annual spikes
in March</em></figcaption>


#### Daily Filings for February--March


<figcaption><em>Daily histogram for February and March across all years,
with the deadline marked</em></figcaption>


It looks like people who have to read these reports don't get to enjoy
spring break.

PHMSA already provides a six-month window for hazardous liquid and CO2
pipelines to file annual reports. Aligning gas and storage reporting
with this standard removes unnecessary regulatory differences and
administrative confusion.

The expanded reporting forms disproportionately burden smaller
operators, who often lack the staff to spread reporting tasks across
multiple teams. For these entities, the extra three months could mean
the difference between a manageable process and one that requires
expensive outside help.

The data are clear: most pipeline operators file their annual reports as
close to the March 15 deadline as possible. Extending the deadline to
June 15 provides tangible benefits --- lower compliance costs, improved
data quality, and a more consistent regulatory environment --- without
any identified downside for public safety.

PHMSA's proposed rule, Pipeline Safety: Adjust Annual Report Filing
Timelines (Docket No. PHMSA--2025--0108; RIN 2137--AF77), is a
pragmatic, evidence-based step that modernizes regulatory practice for a
more complex energy landscape.
::::### Appendix: Data and Replication Code 

Below is the full Python code to reproduce the time series and daily
filing histogram from the empirical analysis. This code uses [public
filings data from
2017--2025](https://www.phmsa.dot.gov/data-and-statistics/pipeline/gas-distribution-gas-gathering-gas-transmission-hazardous-liquids).

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# Load your merged data
df = pd.read_parquet('UNGS AR 2017-2025.parquet')
df['FILING_DATE'] = pd.to_datetime(df['FILING_DATE'], errors='coerce')
# --- Plot 1: Monthly filings over 7 years ---
df['filing_ym'] = df['FILING_DATE'].dt.to_period('M')
monthly_counts = df.groupby('filing_ym').size().reset_index(name='report_count')
plt.figure(figsize=(12, 5))
plt.plot(monthly_counts['filing_ym'].astype(str), monthly_counts['report_count'], marker='o', linewidth=2)
plt.xticks(rotation=90)
plt.ylabel('Number of Reports')
plt.xlabel('Year-Month')
plt.title('Monthly Underground Natural Gas Storage Filings (2018–2025)')
plt.tight_layout()
plt.savefig('annual_report_monthly_timeline.png')
plt.show()
# --- Plot 2: Daily histogram for Feb–Mar, all years ---
df_initial = df[df['REPORT_SUBMISSION_TYPE'] == 'INITIAL']
feb_mar = df_initial[
    (df_initial['FILING_DATE'].dt.month.isin([2, 3])) &
    (df_initial['FILING_DATE'].dt.day >= 1) &
    (df_initial['FILING_DATE'].dt.day <= 31)
]
feb_mar['plot_day'] = (feb_mar['FILING_DATE'].dt.month - 2) * 31 + feb_mar['FILING_DATE'].dt.day
pivot = feb_mar.groupby([feb_mar['FILING_DATE'].dt.year, 'plot_day']).size().unstack(fill_value=0)
avg_counts = pivot.mean(axis=0)
plt.figure(figsize=(12, 6))
for year in pivot.index:
    plt.plot(pivot.columns, pivot.loc[year], color='#cccccc', alpha=0.7, linewidth=1)
plt.plot(pivot.columns, avg_counts, color='black', linewidth=2, label='Average')
plt.axvline(44, color='red', linestyle='--', linewidth=2, label='March 15')  # March 15 = (3-2)*31 + 15 = 44
tick_days = [1, 8, 15, 22, 29, 44, 59]
tick_labels = ['Feb 1', 'Feb 15', 'Mar 1', 'Mar 15', 'Mar 31']
plt.xticks(tick_days, tick_labels)
plt.ylabel('Initial Report Filings')
plt.xlabel('Date')
plt.title('Initial Report Filings: Feb 1–Mar 31 (All Years)')
plt.legend()
plt.tight_layout()
plt.savefig('initial_reports_feb_mar_all_years.png')
plt.show()
```
::::**References**

- Pipeline and Hazardous Materials Safety Administration. *Pipeline
  Safety: Adjust Annual Report Filing Timelines*. 49 CFR Part 191.
  Docket No. PHMSA--2025--0108; RIN 2137--AF77. July 1, 2025.
- Federal Register Vol. 90, №124 / Tuesday, July 1, 2025.
::::::::::::::::By [Kyle Jones](https://medium.com/@kyle-t-jones) on
[July 31, 2025](https://medium.com/p/d71506702a60).

[Canonical
link](https://medium.com/@kyle-t-jones/pipeline-safety-reporting-moving-the-annual-report-deadline-from-march-15-to-june-15-d71506702a60)

Exported from [Medium](https://medium.com) on November 10, 2025.
