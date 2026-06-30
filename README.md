# 📊 Google Play Store Data Visualization







***

## 📁 Project Overview

This project is a series of **6 interactive data visualization tasks** built on the **Google Play Store dataset**. Each task produces a unique chart type with specific data filters, category translations, and a **time-gate mechanism** that restricts chart visibility to a defined IST window. All visualizations are built using **Plotly** inside **Jupyter Notebooks** with a consistent dark theme.

***

## 📂 Repository Structure

```
google-playstore-visualization/
│
├── data/
│   └── googleplaystore.csv          ← Source dataset (Google Play Store)
│
├── task1.ipynb
│
├── task2_.ipynb
│
├── task3.ipynb
│
├── task4.ipynb
│
├── task5.ipynb
│
├── task6.ipynb
│
└── README.md
```

***

## 🛠️ Tech Stack

| Library | Version | Purpose |
|---|---|---|
| Python | 3.9+ | Core language |
| Pandas | 2.x | Data manipulation and filtering |
| NumPy | 1.x | Numerical operations and simulation |
| Plotly | 5.x | Interactive chart rendering |
| zoneinfo | Built-in (3.9+) | IST timezone handling for time gates |
| Jupyter Notebook | 6.x+ | Notebook environment |

***


***

## ⏱️ Time Gate Summary

Every task has a time-gate that restricts chart visibility to a specific IST window. Outside the window, a restriction message is displayed instead of the chart.

| Task | Chart Type | Time Window (IST) |
|---|---|---|
| Task 1 | Grouped Bar Chart | 3:00 PM – 5:00 PM |
| Task 2 | Choropleth Map | 6:00 PM – 8:00 PM |
| Task 3 | Dual-Axis Bar Chart | 1:00 PM – 2:00 PM |
| Task 4 | Time Series Line Chart | 6:00 PM – 9:00 PM |
| Task 5 | Bubble Chart | 5:00 PM – 7:00 PM |
| Task 6 | Stacked Area Chart | 4:00 PM – 6:00 PM |

All time gates use Python's built-in `zoneinfo` module — no external library installation required.

```python
from zoneinfo import ZoneInfo
from datetime import datetime
ist_now      = datetime.now(ZoneInfo("Asia/Kolkata"))
current_hour = ist_now.hour
```

***

***

## ✅ Task 1 — Grouped Bar Chart

**File:** `task1_grouped_bar_chart/task1_grouped_bar_chart.ipynb`
**Time Gate:** 3:00 PM – 5:00 PM IST

### Objective
Compare the **average rating** and **total review count** for the **top 10 app categories** by number of installs using a grouped bar chart.

### Filters Applied

| Filter | Condition |
|---|---|
| Average Rating | ≥ 4.0 |
| App Size | ≥ 10 MB |
| Last Updated Month | January only |

### Steps Performed

1. Loaded the Google Play Store dataset
2. Converted `Size` column to numeric (MB) and `Last Updated` to datetime
3. Applied all 3 filters simultaneously using Pandas boolean indexing
4. Grouped data by `Category` → summed installs → selected `nlargest(10)`
5. Aggregated top 10 categories to compute `Avg Rating` and `Total Reviews`
6. Built a grouped bar chart using `plotly.graph_objects` with two bar traces per category
7. Applied time gate: chart renders only between 3 PM and 5 PM IST

### Chart Features
- Two bars per category: one for Average Rating, one for Total Review Count
- Dual Y-axis: left for rating, right for review count
- Value labels on top of each bar
- Hover tooltips with exact values
- Dark theme consistent across all tasks
- Full filter details shown in chart subtitle


***

## ✅ Task 2 — Choropleth Map

**File:** `task2_choropleth_map/task2_choropleth_map.ipynb`
**Time Gate:** 6:00 PM – 8:00 PM IST

### Objective
Visualize **global installs by app category** using an interactive Choropleth map showing country-level install distribution.

### Filters Applied

| Filter | Condition |
|---|---|
| App Category | Must NOT start with A, C, G, or S |
| Top 5 categories | Selected by total install count after filtering |
| Country highlight | Red border on countries with installs > 1 Million |

### Category Exclusions
The following categories were removed because they start with A, C, G, or S:
- ❌ ART_AND_DESIGN (starts with A)
- ❌ COMMUNICATION (starts with C)
- ❌ GAME (starts with G)
- ❌ SHOPPING (starts with S)
- ❌ SOCIAL (starts with S)

### Top 5 Categories Selected
TOOLS, ENTERTAINMENT, PRODUCTIVITY, VIDEO_PLAYERS, FAMILY

### Special Notes
- The Google Play Store dataset does not contain country-level install data
- Country-wise install data was **simulated realistically** using population and smartphone penetration multipliers across **48 countries**

### Chart Features
- Dark-themed world map with Natural Earth projection
- Blue color scale: deeper blue = higher installs
- **Red border** on countries where installs exceed 1 Million
- Dropdown menu to switch between all 5 categories interactively
- Hover tooltip showing Country, Installs (Millions), Category, and >1M flag
- `fig.update_geos()` used separately from `fig.update_layout()` to avoid Plotly 5.x `ValueError`


***

## ✅ Task 3 — Dual-Axis Bar Chart

**File:** `task3_dual_axis_chart/task3_dual_axis_chart.ipynb`
**Time Gate:** 1:00 PM – 2:00 PM IST

### Objective
Compare **average installs** and **average revenue** for **Free vs Paid apps** within the **top 3 app categories** using a dual-axis grouped bar chart.

### Filters Applied

| Filter | Condition |
|---|---|
| Installs | ≥ 10,000 |
| Revenue | ≥ $10,000 |
| Android Version | > 4.0 (numeric comparison) |
| App Size | > 15 MB |
| Content Rating | = "Everyone" only |
| App Name Length | ≤ 30 characters (spaces + special chars included) |

### Helper Columns Created

| Column | How Created | Purpose |
|---|---|---|
| `Android_Ver_Float` | `pd.to_numeric(Android_Ver, errors='coerce')` | Converts "4.1" → 4.1 for `> 4.0` comparison |
| `App_Name_Len` | `df['App'].str.len()` | Counts every character including spaces and symbols |

### Chart Features
- **4 bars per category group**: Free Installs (Blue), Paid Installs (Green), Free Revenue (Purple), Paid Revenue (Gold)
- **Left Y-axis**: Average Installs
- **Right Y-axis**: Average Revenue in USD (`overlaying='y', side='right'`)
- Value labels on top of every bar
- Hover tooltips with exact values
- Horizontal legend at bottom
- `barmode='overlay'` with manual x-position offsets for grouped appearance


***

## ✅ Task 4 — Time Series Line Chart

**File:** `task4_timeseries/task4_timeseries.ipynb`
**Time Gate:** 6:00 PM – 9:00 PM IST

### Objective
Show the **trend of total installs over time** (Jan 2021 – Dec 2023) segmented by app category with **month-over-month growth highlighting** for months where installs grew more than 20%.

### Filters Applied

| Filter | Condition |
|---|---|
| App Name | Must NOT start with X, Y, or Z |
| App Name | Must NOT contain the letter "S" anywhere (case-insensitive) |
| App Category | Must start with "E", "C", or "B" only |
| Reviews | > 500 |

### Category Translations (Displayed in Chart Legend)

| Original | Displayed As | Language |
|---|---|---|
| BEAUTY | BEAUTY (सौंदर्य) | Hindi |
| BUSINESS | BUSINESS (வணிகம்) | Tamil |
| All others | Original name | — |

> **Note on Dating category:** Dating starts with the letter D, not E, C, or B — so it was correctly excluded by the category filter.

### MoM Growth Highlighting Logic
```python
monthly['MoM_Growth'] = monthly.groupby('Category')['Installs'].pct_change() * 100
monthly['High_Growth'] = monthly['MoM_Growth'] > 20
```
Months where MoM growth exceeded 20% are marked with ⭐ star markers and faint yellow vertical bands.

### Chart Features
- One colored line per category across 36 months
- ⭐ Star markers on every month with > 20% MoM growth
- Faint yellow vertical shading bands on high-growth months
- `hovermode='x unified'` to show all categories on single hover
- `tickformat='%b %Y'` for clean month labels
- Translation annotations at bottom of chart


***

## ✅ Task 5 — Bubble Chart

**File:** `task5_bubble_chart/task5_bubble_chart.ipynb`
**Time Gate:** 5:00 PM – 7:00 PM IST

### Objective
Analyze the relationship between **app size (MB)** and **average rating**, with **bubble size representing total installs**, across 9 specific app categories.

### Filters Applied

| Filter | Condition |
|---|---|
| Rating | > 3.5 |
| App Category | GAME, BEAUTY, BUSINESS, COMICS, COMMUNICATION, DATING, ENTERTAINMENT, SOCIAL, EVENTS |
| Reviews | > 500 |
| App Name | Must NOT contain letter "S" (upper or lower case) |
| Sentiment Subjectivity | > 0.5 |
| Installs | > 50,000 |

### Category Translations (Displayed in Chart Legend)

| Original | Displayed As | Language |
|---|---|---|
| BEAUTY | BEAUTY (सौंदर्य) | Hindi |
| BUSINESS | BUSINESS (வணிகம்) | Tamil |
| DATING | DATING (Partnersuche) | German |

### Special Highlight
- **GAME category** bubbles are highlighted in **Pink (#ff69b4)** with a deep pink glowing border
- GAME category label is also rendered in pink color

### Bubble Size Normalization
```python
# Scales Total Installs to pixel diameter range 20–100
agg['Bubble_Size'] = 20 + ((installs - min) / (max - min)) * 80
```

### Aggregation Per Category
Each bubble represents one entire category (not individual apps):
- **X position** → Average App Size (MB)
- **Y position** → Average Rating
- **Bubble diameter** → Total Installs (normalized)

### Chart Features
- 9 bubbles — one per category
- GAME = Pink (#ff69b4) with deep pink border
- Dashed yellow reference line at Rating = 4.0
- Dotted blue reference line at Size = 50 MB
- Hover tooltip: Avg Size, Avg Rating, Total Installs, App Count
- Translation note annotation at bottom

### Notebook Cells


***

## ✅ Task 6 — Stacked Area Chart

**File:** `task6_stacked_area/task6_stacked_area.ipynb`
**Time Gate:** 4:00 PM – 6:00 PM IST

### Objective
Visualize the **cumulative number of installs over time** for each app category as a stacked area chart, highlighting months where any category's installs grew more than 25% month-over-month.

### Filters Applied

| Filter | Condition |
|---|---|
| Average Rating | ≥ 4.2 |
| App Name | Must NOT contain any digits (0–9) |
| App Category | Must start with "T" or "P" only |
| Reviews | > 1,000 |
| App Size | Between 20 MB and 80 MB |

### Categories After Filter (T and P only)
TOOLS, TRAVEL_AND_LOCAL, PRODUCTIVITY, PHOTOGRAPHY, PARENTING

### Category Translations (Displayed in Chart Legend)

| Original | Displayed As | Language |
|---|---|---|
| TRAVEL_AND_LOCAL | Travel & Local → Voyage & Local (FR) | 🇫🇷 French |
| PRODUCTIVITY | Productivity → Productividad (ES) | 🇪🇸 Spanish |
| PHOTOGRAPHY | Photography → 写真撮影 (JA) | 🇯🇵 Japanese |

### Digit Filter Implementation
```python
# r'\d' is regex for any digit 0–9
df = df[~df['App'].str.contains(r'\d', na=False)]
```

### MoM Growth Highlighting Logic
```python
monthly['MoM_Growth']        = monthly.groupby('Category')['Installs'].pct_change() * 100
monthly['High_Growth']        = monthly['MoM_Growth'] > 25
monthly['Cumulative_Installs'] = monthly.groupby('Category')['Installs'].cumsum()
```
Months with > 25% MoM growth across **any** category are highlighted with faint yellow vertical bands and dotted borders.

### Chart Features
- Stacked area using `fill='tonexty'` and `stackgroup='one'`
- One color band per category (stacked cumulatively)
- Yellow vertical shading bands on high-growth months
- Arrow annotation pointing to first high-growth month
- `hovermode='x unified'` to show all categories on single hover
- Three translation annotations at the bottom of the chart
- 36-month time range (Jan 2021 – Dec 2023)


***

## 🌍 All Category Translations Used Across Tasks

| Task | Category | Translation | Language |
|---|---|---|---|
| Task 4 | BEAUTY | सौंदर्य | Hindi |
| Task 4 | BUSINESS | வணிகம் | Tamil |
| Task 5 | BEAUTY | सौंदर्य | Hindi |
| Task 5 | BUSINESS | வணிகம் | Tamil |
| Task 5 | DATING | Partnersuche | German |
| Task 6 | TRAVEL_AND_LOCAL | Voyage & Local | French |
| Task 6 | PRODUCTIVITY | Productividad | Spanish |
| Task 6 | PHOTOGRAPHY | 写真撮影 | Japanese |

***

## 🔁 Common Patterns Used Across All Tasks

### Time Gate Pattern (used in all 6 tasks)
```python
from datetime import datetime
from zoneinfo import ZoneInfo

ist_now      = datetime.now(ZoneInfo("Asia/Kolkata"))
current_hour = ist_now.hour

if START_HOUR <= current_hour < END_HOUR:
    fig.show()
else:
    print("⏱ CHART ACCESS RESTRICTED")
    print(f"Available: {START_HOUR}:00 – {END_HOUR}:00 IST")
    print(f"Current IST time: {ist_now.strftime('%I:%M:%S %p')}")
```

### Category Translation Pattern (Tasks 4, 5, 6)
```python
translation_map = {
    'BEAUTY'   : 'BEAUTY (सौंदर्य)',
    'BUSINESS' : 'BUSINESS (வணிகம்)',
}
df['Category_Display'] = df['Category'].map(translation_map).fillna(df['Category'])
```

### MoM Growth Calculation (Tasks 4, 6)
```python
monthly['MoM_Growth'] = (
    monthly.groupby('Category')['Installs']
    .pct_change() * 100
)
monthly['High_Growth'] = monthly['MoM_Growth'] > THRESHOLD
```

***


## 📊 Dataset Information

| Property | Detail |
|---|---|
| Dataset | Google Play Store Apps |
| Source | Kaggle — Google Play Store Dataset |
| Rows | ~10,000 apps |
| Key Columns | App, Category, Rating, Reviews, Installs, Size, Type, Price, Content Rating, Last Updated, Current Ver, Android Ver |

> **Note:** The dataset does not contain `Revenue` or `Sentiment_Subjectivity` columns natively. These were simulated in Tasks 3 and 5 respectively. Country-level install data used in Task 2 was also simulated using population-based multipliers across 48 countries.

***

## 👩‍💻 Author

**Priya**
Built with Python, Plotly, Pandas, and Jupyter Notebook

***
