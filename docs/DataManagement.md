# Data Management

## Opportunity
Records in multiple datasets need to be matched when a primary key is missing. Or entities within a data set needs to be de-duplicated but matching records do not have the same key.

## Value Statement
Deepen customer or patient insights; uncover errors in invoices or other large transactional documents to realize lost revenue or support compliance. This can be achieved by improving master data management. The following fuzzy matching techniques can be used to bring together multiple data sources to enhance or de-duplicate records even if they don't have a common key or are pulling from unstructured data. Examples include:

- Match tax records to payment records for IRS verification
- Get a better picture of retail customers by matching records from multiple systems
- De-duplicate master data to improve the quality of your data
- Reduce time spent on manually matching records or manual de-duplication
- Collect longitudinal patient information to better provide coordinated care

## Architecture

### Fuzzy Matching with Structured Data

![Fuzzy Matching with Structured Data](/docs/fuzzy_matching_with_structured_data.png)

The architecture for fuzzy matching for data management with structured data will generally follow the pattern for batch ingestion and enrichment of structured data. In the simplest example, this will involve:

1. Ingest: Batch ingestion of data from line of business (LOB) applications via Azure Data Factory.
2. Store: Storing the data as structured tables in an Azure SQL database.
3. Fuzzy Matching: For non-Spark fuzzy matching, a Python Azure Function can be used as the scalable compute target or for Spark-based fuzzy matching Azure Databricks can be leveraged as the scalable compute target.
4. Serve: Fuzzy matched or de-duplicated data will then sent back to the Azure SQL database to be consumed by downstream applications and BI reports.

### Fuzzy Matching with Structured and Unstructured Data

![Fuzzy Matching with Structured and Unstructured Data](/docs/fuzzy_matching_with_structured_and_unstructured_data.png)

