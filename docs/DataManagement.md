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

To incorporate unstructured data to a fuzzy matching architecture, you can add the general knowledge mining architecture to the ingestion process. This architecture that incorporates both structured and unstructured documents involves the following:

1. **Ingest:** Batch ingestion of structured from line of business (LOB) applications and unstructured documents via Azure Data Factory.
2. **Store & Enrich:** Storing structured data as tables in Azure SQL database. Storing unstructured documents in Azure Storage (blob or Azure Data Lake Storage, depending on security requirements). Unstructured documents are indexed and enriched via Azure Cognitive Search and the results are persisted back to the storage account. Visit the Knowledge Mining Wiki  for further details.
3. **Fuzzy Matching:** For non-Spark fuzzy matching, a Python Azure Function can be used as the scalable compute target or for Spark-based fuzzy matching Azure Databricks can be leveraged as the scalable compute target.
4. **Serve:** Fuzzy matched or de-duplicated data will then sent back to the Azure SQL database to be consumed by downstream applications and BI reports.

The actual architecture will depend on the needs of your customer but this is the most recommended and lightweight solution. Azure Synapse Analytics spark pools were not recommended as a Spark compute engine because they will not be as performant as Databricks and fuzzy matching is a highly performance-intensive task. A fully integrated architecture with Azure Synapse Analytics using Azure Synapse SQL pools, Spark pools, data flows, and Power BI represents a more streamlined but potentially less performant alternative.

## Techniques

There are many different techniques for using fuzzy matching for matching records or de-duplication. Fundamentally, how fuzzy matching works is by finding the best approximate match on one or more fields when there is not a shared primary or composite key. These are often based on text fields but can also be applied to numeric or encoded fields, geographic fields, or date/time fields. Different strategies should be considered based on the type of field, the source of distance or dissimilarity (i.e. misspellings, different formats for addresses/dates), and considerations for scalability.

### String Similarity Algorithms

The most common approach to fuzzy matching is to calculate the similarity between 2 strings. In its simplest form, this approach takes each string, and compares it to every other string within the dataset (for de-duplication) or to the string entity in the other dataset (for entity linking) and calculates a string similarity score based on one of many string similarity/distance algorithms. A threshold can then be set for defining a "match". The different types of partial string matching algorithms are listed below:

### Common Techniques

- **Edit Based:** compute the number of operations needed to transform one string to another. Levenshtein distance is the most commonly used technique in this category
Best for: Comparing strings with misspellings that are generally single character insertions, deletions, or substitutions. For example: matching the word "successful" to the misspelled version "sucessful"
- **Token Based:** based on an input set of tokens, finds the similar tokens between both sets. Generally used by transforming a sentence into a token of words or n-grams.
Best for: Comparing strings that have multiple character or word-level insertions, deletions or substitutions at a time. Generally more performant than Edit Distance methods. For example: matching the strings "Contoso Corp Inc", "Contoso Corp" and "Contoso Corp LLC" where the "Inc" or "LLC" are insertions of a word being added rather than just single character insertions.
- **Sequence Based:** based on common sub-strings between the 2 strings. Tries to find the longest sequence present in both strings.
Best for: Comparing strings with multiple words where substring matches are the most important to match on. For example: matching a location named "Golden Gate Park: A large urban park near the Golden Gate bridge" with "Golden Gate Park: California's most visited park located next to the Golden Gate Bridge" where "Golden Gate Park" and "Golden Gate Bridge" would be substrings matched on.

###Advanced Techniques

- **Phonetic Based:** computes similarity based on how strings phonetically sound.
Best for: matching strings that come from transcribed audio. For example: matching the name "Jesus" to misspelled transcribed version "Heyzeus".
- **Word Embeddings:** leverages word embeddings such as BERT  or Word2Vec  to incorporate the semantic meaning of words by first converting strings into a vector representation where similiar words have similar weighting.
Best for: when the meaning of the words are important. For example: matching "123 main street" to "123 main road" where "street" and "road" are not misspellings but actually words with similar meanings
- **Compression Based:** calculates the distance of compressed strings using normalized compression distance (NCD)  using different compression algorithms.
Best for: comparing strings where order and repetition doesn't matter. This article  explains this concept well. For example: matching "000000005617" to "005167" to "05167" would be considered different under edit distance but perfect matches under NCD.




