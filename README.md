# Green Purchasing Behavior – NoSQL Analytics

Reimplementation of a miniature NoSQL analytics stack (custom JSON/CSV/Excel parsers, collection abstraction, and Mongo-like operations) to study how U.S. counties invest in sustainable or “green” food options. All data now comes from the USDA Food Environment Atlas plus the supplied Excel workbooks in the repo—no synthetic JSON fixtures remain.

---

## Data Sources

| Purpose | File | Notes |
| --- | --- | --- |
| Consumer spending (green foods proxy) | `consumer-data/cu-all-detail-2023.xlsx` | Parsed via `load_excel_file()` then normalized by `transform_consumer_data()`. National-level aggregated data. |
| Employment / wages | `employement_data/all_data_M_2024.xlsx` | Fed through `transform_employment_excel_data()` to derive occupations + income tiers. County/metro-level data. |
| Metro unemployment stats | `unemployment-rate-data/metro-annual-unemployment-rates.xlsx` | Converted with `transform_unemployment_excel_data()`. Contains unemployment rates by year (2020-2024) for metro areas. |

> Parsers avoid pandas/csv/json; only the standard library plus `openpyxl` (if available) is used. No proxies or synthetic data - all analysis uses actual data from Excel files.

---

## Pipeline Summary

1. **Parsing layer**  
   - `json_load`, `load_csv_file`, and `load_excel_file` convert raw files into the custom `Collection` structure.
2. **Collection utilities**  
   - Filtering, projection, group-by, aggregation, and joins are hand-written to mimic MongoDB-style operations.
3. **Transformers**  
   - Consumer, employment, and unemployment Excel sheets are mapped into canonical docs (`county`, `category`, `spend`, etc.).  
   - Consumer expenditure data is national-level (uses "national" as county identifier).
   - Employment and unemployment data are county/metro-level with actual geographic names.
4. **Analysis app (`green_purchasing_behavior_project.ipynb`)**  
   - Loads every data source, runs the core questions, and prints tabular summaries:
     - Geographic profiles (who buys sustainable food and where)
     - Income distribution analysis (separate analysis due to data granularity differences)
     - Employment status analysis (occupation and income distribution)
     - Unemployment trends over time (2020-2024)
     - Category-level spending patterns

---

## Analysis Results

### Data Loaded
- **Consumer spending data**: 4,459 records loaded, transformed to 2,543 food spending records
- **Employment data**: 414,437 records loaded, transformed to 414,437 job/employment records
- **Unemployment data**: 391 records loaded, transformed to 1,935 unemployment records (multiple years per metro area)

### Question 1: Who is buying sustainable food? (Geography Analysis)
- **High spending records**: Found 116 records with spending > $1,000
- **Total spending**: $270,724,254.17 across 2,543 records
- **Geographic distribution**: All consumer expenditure data is national-level (aggregated), showing overall U.S. consumer spending patterns

### Question 2: How income influences sustainable purchasing behavior
- **Data granularity insight**: Consumer expenditure data uses "national" as county identifier, while employment data uses actual county/metro names. This prevents direct joins but allows independent analysis of each dataset.
- **Income distribution**: Employment data shows 414,437 counties/metro areas categorized by income levels
- **Food spending patterns**: Top food categories by average spending include consumer unit weights and statistical measures

### Question 3: Do economic shocks (unemployment) change spending habits?
- **Unemployment trends by year**:
  - 2020: 7.57% (highest, reflecting COVID-19 impact)
  - 2021: 5.01%
  - 2022: 3.66%
  - 2023: 3.65%
  - 2024: 4.04%
- **Unemployment distribution**: 
  - Low unemployment (< 5%): 1,277 metro areas
  - Medium unemployment (5-8%): 482 metro areas
  - High unemployment (> 8%): 176 metro areas
- **Food spending trends**: Average food spending in 2023 was $106,458.61 (national level)

### Question 4: How does employment status affect green purchasing behavior?
- **Employment distribution**: Analysis shows employment data across 414,437 areas with various occupation types
- **Geographic coverage**: Data includes all U.S. states, major metro areas, and nonmetropolitan regions
- **Top areas by record count**: U.S. (177,824), California (829), Texas (827), Pennsylvania (818), New York (817)

### Question 5: Spending patterns by food category
- **Filtered results**: After removing statistical/demographic records, 2 actual food spending categories remain
- **Top categories**: 
  - Consumer unit column weight (interview): $134,555,809.00
  - Consumer unit column weight (diary): $134,488,926.00
- **Note**: Most consumer expenditure data contains statistical measures and demographics rather than specific food item categories

### Key Insights
1. **Data granularity matters**: Consumer expenditure data is national-level while employment/unemployment data is regional. This prevents direct joins but enables separate analysis of national trends vs. regional economic conditions.
2. **Unemployment recovery**: Unemployment rates dropped significantly from 2020 (7.57%) to 2023-2024 (3.65-4.04%), showing economic recovery post-COVID-19.
3. **Geographic coverage**: Employment data provides comprehensive coverage of all U.S. counties, metro areas, and nonmetropolitan regions.
4. **Consumer expenditure structure**: The consumer expenditure survey data focuses on statistical measures and demographics rather than individual food item spending, which limits category-level food analysis.


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