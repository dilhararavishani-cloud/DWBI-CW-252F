# Multi-Source Sales ETL Pipeline

A pandas-based ETL pipeline that extracts, cleans, integrates, and models sales, customer, and product data into a star-schema analytics dataset.

## Pipeline Phases

1. **Transform — Sales**: Standardizes columns, fixes types, keeps rows with missing `unit_price` (to be enriched later) instead of rejecting them.
2. **Data Quality**: Profiles `sales_transformed_df` for missing values, duplicate `order_id`s, and cardinality issues.
3. **Data Modeling — Date Dimension**: Builds `dim_date` from unique order dates.
4. **Integration**: Joins sales with products (`sales_integrated_df`) and customers to build `analytics_df` / `sales_analytics`.
5. **Date Dimension (analytics)**: Builds a full continuous `dim_date` from min/max order date.
6. **Fact Table**: Builds `fact_sales` with surrogate keys from `dim_customer`, `dim_product`, `dim_date`.
7. **Load**: Writes cleaned CSVs to `/content/etl_output/`.
8. **Validation**: Runs row-count and consistency checks (`PASS`/`WARNING`).
9. **Business Analysis**: Computes totals (sales, quantity, orders, unique customers) and monthly sales trend.
10. **Visualization**: Plots monthly sales trend with matplotlib.
11. **Final Load & Summary**: Re-saves cleaned data and prints an end-to-end pipeline summary.

## Inputs
- `sales.csv`
- `products` data (raw/extracted)
- `customers` data (raw/extracted)

## Outputs (`/content/etl_output/`)
- `cleaned_sales.csv`
- `cleaned_customers.csv` (and other cleaned dimension/fact tables)

## Requirements
- Python 3
- pandas, numpy, matplotlib

## Notes
- Missing `unit_price` in raw sales data is **not** treated as a rejection reason — it's enriched later via a join with product data.
- Some later cells (Phases 6.1–14) rework earlier integration steps with added `NameError` guards to enforce run order (e.g., `products_lookup`, `customers_lookup` must exist before dimension-table creation).
