# Global Education & Literacy Rate Trends

> Cleaning 70+ years of cross-country literacy data with Python (Pandas) to analyse modern educational growth.

Author: Glory Chukwudi  
**Tools Used:** Python (Pandas), Google Colab, Our World in Data  

---

## 1. Why I Did This Project
I wanted to understand how global literacy rates have changed over time across different countries. We often hear that education is improving globally, but I wanted to see the actual numbers for myself and find out which countries have made the biggest progress in recent history.

When I downloaded the data from Our World in Data, I quickly realized that raw data is rarely ready to use. It was mixed up with regions, had huge gaps in years, and went as far back as 1475. So, my goal was simple: clean up the mess, focus on the modern era (from 1950 till date), and see which countries truly transformed their education.

## 2. The Problems I Found in the Raw Data
Before I could analyze anything, I had to fix three main issues:

- **Regions Mixed with Countries:** The dataset had 2,167 rows, but 277 of them were not actual countries. They were whole regions like 'Sub-Saharan Africa' or 'World'. I had to separate them so they wouldn't ruin my country rankings.
- **Too Far Back in Time:** Some European countries like Germany and France had records starting from the year 1475. Comparing 500 years of progress in Germany to 70 years in a developing nation isn't fair. I decided to start everyone at 1950 to focus on modern changes.
- **Uneven Years:** Countries did not collect data every single year. Some jumped from 1950 straight to 1979 or 2010. I needed my code to automatically find each country's earliest year and latest year without breaking.

## 3. How I Cleaned the Data in Python

First, I filtered out general regional aggregates by keeping only rows with valid ISO country codes:

```python
# Clean column names
df.columns = df.columns.str.lower().str.replace(' ', '_')

# Keep only rows that have country codes
df_countries = df[df['code'].notna()].copy()

# Focus on 1950 to present
df_modern = df_countries[df_countries['year'] >= 1950].sort_values(by=['code', 'year'])

# Get start rate, latest rate, and calculate total gain
modern_summary = df_modern.groupby(['entity', 'code']).agg(
    start_year=('year', 'min'),
    start_rate=('literacy_rate', 'first'),
    latest_year=('year', 'max'),
    latest_rate=('literacy_rate', 'last')
).reset_index()

# Calculate percentage point increase
modern_summary['point_change'] = (modern_summary['latest_rate'] - modern_summary['start_rate']).round(2)
```

## 4. What the Data Revealed

Once the data was clean, the results highlighted remarkable educational growth:

| Country | Start Year | Start Rate | Latest Year | Latest Rate | Point Increase |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Saudi Arabia** | 1950 | 3.00% | 2024 | 97.93% | **+94.93** |
| **Oman** | 1950 | 3.00% | 2022 | 97.34% | **+94.34** |
| **Qatar** | 1950 | 7.50% | 2024 | 99.32% | **+91.82** |
| **Bahrain** | 1950 | 12.50% | 2024 | 97.82% | **+85.32** |
| **Papua New Guinea** | 1950 | 7.50% | 2022 | 87.00% | **+79.50** |
| **Kuwait** | 1950 | 17.50% | 2020 | 96.46% | **+78.96** |
| **Vietnam** | 1950 | 17.50% | 2022 | 96.13% | **+78.63** |
| **Indonesia** | 1950 | 17.50% | 2020 | 96.00% | **+78.50** |
| **Jordan** | 1950 | 17.50% | 2024 | 94.44% | **+76.94** |
| **Iran** | 1950 | 12.50% | 2023 | 88.92% | **+76.42** |

### **Main Takeaway:** 
Countries like Saudi Arabia, Oman, and Qatar went from under 10% literacy in 1950 to over 97% today. Also, Asian countries like Vietnam and Indonesia helped almost 80% of their population learn to read and write in just two generations. This shows that focused government effort and educational investments can completely change a nation's story in a short time.
