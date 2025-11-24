# Green Purchasing Behavior – NoSQL Analytics

Reimplementation of a miniature NoSQL analytics stack (custom JSON/CSV/Excel parsers, collection abstraction, and Mongo-like operations) to study how U.S. counties invest in sustainable or “green” food options. All data now comes from the USDA Food Environment Atlas plus the supplied Excel workbooks in the repo—no synthetic JSON fixtures remain.

---

## Data Sources

| Purpose | File | Notes |
| --- | --- | --- |
| Consumer spending (green foods proxy) | `consumer-data/cu-all-detail-2023.xlsx` | Parsed via `load_excel_file()` then normalized by `transform_consumer_data()` |
| Employment / wages | `employement_data/all_data_M_2024.xlsx` | Fed through `transform_employment_excel_data()` to derive occupations + income tiers |
| Metro unemployment stats | `unemployment-rate-data/metro-annual-unemployment-rates.xlsx` | Converted with `transform_unemployment_excel_data()` |
| Food Environment Atlas reference | `food_data/StateAndCountyData.csv` | Supplementary context, also used to build additional employment indicators |

> Parsers avoid pandas/csv/json; only the standard library plus `openpyxl` (if available) is used.

---

## Pipeline Summary

1. **Parsing layer**  
   - `json_load`, `load_csv_file`, and `load_excel_file` convert raw files into the custom `Collection` structure.
2. **Collection utilities**  
   - Filtering, projection, group-by, aggregation, and joins are hand-written to mimic MongoDB-style operations.
3. **Transformers**  
   - Consumer, employment, and unemployment Excel sheets are mapped into canonical docs (`county`, `category`, `spend`, etc.).  
   - Food Environment Atlas data augments employment proxies (employment level, metro status, poverty-based rates).
4. **Analysis app (`green_purchasing_behavior_project.ipynb`)**  
   - Loads every data source, runs the core questions, and prints tabular summaries:
     - Geographic profiles (who buys sustainable food and where)
     - Income vs. spending
     - Employment status vs. green purchases
     - Poverty / unemployment shocks vs. spending
     - Category-level spending leaders

---

## Key Findings (current run)

* Counties with **employment rates above 90%** exhibit noticeably higher sustainable-food spending averages (per the Excel-derived employment proxies).
* **High-income counties (median income ≥ \$40k)** dominate total spend, but the employment-level breakdown shows service/retail occupations closing the gap when metro access is strong.
* **Poverty spikes (rate > 15%)** still correlate with lower organic/green purchases, yet several metro areas counteract this via high specialized-store density (per Food Environment Atlas indicators).
* **Top category combinations** (by total dollars) remain concentrated in urban California, New York, and Alaska boroughs thanks to their large direct-sales and farmers-market footprints.

> Rerun the notebook to regenerate outputs after refreshing source data; the README is aligned with the Excel-based pipeline.

---

## How to Reproduce

1. Install Python 3.10+ and (optionally) `openpyxl`:
   ```bash
   pip install openpyxl
   ```
2. Launch Jupyter and open `green_purchasing_behavior_project.ipynb`.
3. Run the notebook top-to-bottom; it will:
   - Parse the Excel + CSV files in-place.
   - Build the custom collections.
   - Emit the analysis tables to stdout.

This README will be updated whenever the analysis logic or upstream data changes the narrative results.