# Solution Overview

The pipeline follows a simple medallion architecture (Bronze → Silver → Gold) with an additional Quarantine layer for data quality management.

```text
orders_raw.csv
        │
        ▼
bronze.orders_raw
        │
        ├────────────► quarantine.orders_invalid
        │
        ▼
silver.orders_clean
        │
        ▼
gold.sales_summary
```

### Bronze Layer
The raw CSV file is ingested into `bronze.orders_raw` without business transformations. Basic metadata such as ingestion timestamp and source file information is added to ensure traceability and auditability.

### Quarantine Layer
Records failing data quality checks (missing mandatory fields, invalid formats, duplicate keys, negative amounts, etc.) are written to `quarantine.orders_invalid` together with the validation reason and quarantine timestamp. This allows investigation of problematic records without interrupting the pipeline.

### Silver Layer
Valid records are cleaned, standardized, and enriched in `silver.orders_clean`. Data types are corrected, formats are normalized, and derived business fields are calculated to create a trusted and analytics-ready dataset.

### Gold Layer
Business-oriented aggregations are created in `gold.sales_summary`, providing a curated reporting dataset optimized for self-service analytics and dashboard consumption.

This approach separates raw, cleansed, and aggregated data while ensuring full traceability, data quality control, and governance throughout the pipeline.
