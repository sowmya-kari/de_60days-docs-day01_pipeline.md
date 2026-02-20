# de_60days-docs-day01_pipeline.md
# Day 1 — Lakehouse Pipeline (Azure + Databricks)

## Use case (2 lines)
Retail orders analytics: build trusted daily revenue + top products dashboards.
Data lands daily, is cleaned/validated, and served to BI with consistent KPIs.
## Sources (pick 3)
1) Azure SQL Database (Orders, Customers)
2) Daily CSV drops to ADLS (Products catalog from vendor)
3) Event Hubs stream (Clickstream / order events)
   
## Bronze (raw Delta) — what + why (2 bullets)
- What: Raw ingested data stored as Delta tables with minimal changes + metadata columns (ingest_ts, source_file).
- Why: Keeps full history for audit/replay and protects downstream from source reprocessing issues.


## Silver (clean Delta) — what + why (2 bullets)
- What: Cleaned/typed/deduped/conformed tables; invalid records routed to quarantine with a reason.
- Why: Creates a trusted layer for analytics and prevents bad data from reaching dashboards.


## Gold (marts) — what + why (2 bullets)
- What: Business-ready tables like sales_daily, top_customers, product_perf with defined grains.
- Why: Fast BI queries + consistent KPI definitions (single source of truth).

## Serving
Databricks SQL / Power BI dashboards.

KPIs (5)
1) Total revenue (daily/MTD)
2) Orders per day
3) Average order value (AOV)
4) Top products by revenue
5) Revenue by city/store


## Failure handling (3 examples)
1) Failure: Daily file missing or late in ADLS  
   Fix/handling: ADF trigger checks + alert; pipeline waits/retries; rerun for that date once file arrives.

2) Failure: Duplicates or bad keys (order_id null, qty <= 0)  
   Fix/handling: Dedupe + validation in Silver; invalid rows go to quarantine; Gold built only from valid Silver.

3) Failure: Schema drift (new column appears in source)  
   Fix/handling: Bronze ingests with schema rescue/evolution; promote column to Silver only after review + contract update.

