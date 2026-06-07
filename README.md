# Multi-Tenant Client Analytics Pipeline (Medallion Architecture)

An enterprise-ready, defensive PySpark data pipeline built on Delta Lake designed to ingest fragmented, multi-format client data stream assets and consolidate them into a unified Gold Star Schema model.

## 🛠️ Architecture Decisions & Implementation

### 1. Medallion Layering Paradigm
- **Bronze:** Abstracted ingestion layer utilizing parameterized configurations to read variable delimiters (CSV, Pipe, TSV) while maintaining raw layout immutability and lineage (`ingested_at`).
- **Silver:** Structural harmonization layer. Dynamically parses name column variations (split vs. collapsed full strings) and maps varying transaction dimensions across multiple tenants.
- **Gold:** Dimension (`dim_customers`) and Fact (`fct_transactions`) presentation layer optimized for down-stream BI and analytics engine operations.

### 2. Defenses & Self-Healing Resilience
- **Type-Casting Safety:** Implemented native SQL `try_cast` evaluations to isolate and cleanly handle malformed alphabetic anomalies (e.g., `'EXTRA'` artifacts or misaligned rows) without triggering cluster execution failures.
- **Data Formatting Normalization:** Utilized robust regex patterns (`regexp_replace`) to strip raw currency formatting syntax (such as embedded `$` symbols) before numeric aggregation processing.
- **Temporal Fallbacks:** Created lineage fallback logic mapping ingestion metadata timestamps as analytical transaction proxies for downstream clients missing native temporal records altogether.
- **Key-Collision Elimination:** Implemented distributed deterministic cryptographic SHA-256 surrogate keys (`customer_sk`, `transaction_sk`), resolving overlapping client ID ranges seamlessly across worker nodes in parallel.

## 🚀 Execution & Verification
Sample tabular evaluation snapshots of the final execution run are fully visible in the `/outputs` directory. The schema and validation integrity can be verified via the cross-entity star-join execution map embedded within `/notebooks/run_pipeline.py`.
