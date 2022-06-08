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

---

## Architecture

### Fuzzy Matching with Structured Data

![Fuzzy Matching with Structured Data](/architecture/fuzzy_matching_data_management_structured_data.png)

The architecture for fuzzy matching for data management with structured data will generally follow the pattern for batch ingestion and enrichment of structured data. In the simplest example, this will involve:

1. **Ingest:** Batch ingestion of data from line of business (LOB) applications via Azure Data Factory.
2. **Store:** Storing the data as structured tables in an Azure SQL database.
3. **Fuzzy Matching:** For non-Spark fuzzy matching, a Python Azure Function can be used as the scalable compute target or for Spark-based fuzzy matching Azure Databricks can be leveraged as the scalable compute target.
4. **Serve:** Fuzzy matched or de-duplicated data will then sent back to the Azure SQL database to be consumed by downstream applications and BI reports.

### Fuzzy Matching with Structured and Unstructured Data

![Fuzzy Matching with Structured and Unstructured Data](/architecture/fuzzy_matching_data_management_structured_and_unstructured_data.png)

To incorporate unstructured data to a fuzzy matching architecture, you can add the general knowledge mining architecture to the ingestion process. This architecture that incorporates both structured and unstructured documents involves the following:

1. **Ingest:** Batch ingestion of structured from line of business (LOB) applications and unstructured documents via Azure Data Factory.
2. **Store & Enrich:** Storing structured data as tables in Azure SQL database. Storing unstructured documents in Azure Storage (blob or Azure Data Lake Storage, depending on security requirements). Unstructured documents are indexed and enriched via Azure Cognitive Search and the results are persisted back to the storage account. Visit the [Knowledge Mining repo](https://github.com/microsoft/dstoolkit-km-solution-accelerator) for further details.
3. **Fuzzy Matching:** For non-Spark fuzzy matching, a Python Azure Function can be used as the scalable compute target or for Spark-based fuzzy matching Azure Databricks can be leveraged as the scalable compute target.
4. **Serve:** Fuzzy matched or de-duplicated data will then sent back to the Azure SQL database to be consumed by downstream applications and BI reports.

The actual architecture will depend on the needs of your customer but this is the most recommended and lightweight solution. Azure Synapse Analytics spark pools were not recommended as a Spark compute engine because they will not be as performant as Databricks and fuzzy matching is a highly performance-intensive task. A fully integrated architecture with Azure Synapse Analytics using Azure Synapse SQL pools, Spark pools, data flows, and Power BI represents a more streamlined but potentially less performant alternative.

---

## Techniques

There are many different techniques for using fuzzy matching for matching records or de-duplication. Fundamentally, how fuzzy matching works is by finding the best approximate match on one or more fields when there is not a shared primary or composite key. These are often based on text fields but can also be applied to numeric or encoded fields, geographic fields, or date/time fields. Different strategies should be considered based on the type of field, the source of distance or dissimilarity (i.e. misspellings, different formats for addresses/dates), and considerations for scalability.

### String Similarity Algorithms

The most common approach to fuzzy matching is to calculate the similarity between 2 strings. In its simplest form, this approach takes each string, and compares it to every other string within the dataset (for de-duplication) or to the string entity in the other dataset (for entity linking) and calculates a string similarity score based on one of many string similarity/distance algorithms. A threshold can then be set for defining a "match". The different types of partial string matching algorithms are listed below:

### Common Techniques

- **Edit Based:** compute the number of operations needed to transform one string to another. Levenshtein distance is the most commonly used technique in this category.
- Best for: Comparing strings with misspellings that are generally single character insertions, deletions, or substitutions. For example: matching the word "successful" to the misspelled version "sucessful"
- **Token Based:** based on an input set of tokens, finds the similar tokens between both sets. Generally used by transforming a sentence into a token of words or n-grams.
- Best for: Comparing strings that have multiple character or word-level insertions, deletions or substitutions at a time. Generally more performant than Edit Distance methods. For example: matching the strings "Contoso Corp Inc", "Contoso Corp" and "Contoso Corp LLC" where the "Inc" or "LLC" are insertions of a word being added rather than just single character insertions.
- **Sequence Based:** based on common sub-strings between the 2 strings. Tries to find the longest sequence present in both strings..
- Best for: Comparing strings with multiple words where substring matches are the most important to match on. For example: matching a location named "Golden Gate Park: A large urban park near the Golden Gate bridge" with "Golden Gate Park: California's most visited park located next to the Golden Gate Bridge" where "Golden Gate Park" and "Golden Gate Bridge" would be substrings matched on.

### Advanced Techniques

- **Phonetic Based:** computes similarity based on how strings phonetically sound.
- Best for: matching strings that come from transcribed audio. For example: matching the name "Jesus" to misspelled transcribed version "Heyzeus".
- **Word Embeddings:** leverages word embeddings such as [BERT](https://arxiv.org/abs/1810.04805) or [Word2Vec](https://en.wikipedia.org/wiki/Word2vec) to incorporate the semantic meaning of words by first converting strings into a vector representation where similiar words have similar weighting.
- Best for: when the meaning of the words are important. For example: matching "123 main street" to "123 main road" where "street" and "road" are not misspellings but actually words with similar meanings
- **Compression Based:** calculates the distance of compressed strings using [normalized compression distance (NCD)](https://en.wikipedia.org/wiki/Normalized_compression_distance#Normalized_compression_distance) using different compression algorithms.
- Best for: comparing strings where order and repetition doesn't matter. [This article](https://web.archive.org/web/20190311173112/https://articles.life4web.ru/eng/ncd/) explains this concept well. For example: matching "000000005617" to "005167" to "05167" would be considered different under edit distance but perfect matches under NCD.

---

## First-Party Azure Solution

[Azure Cognitive Search](https://docs.microsoft.com/en-us/azure/search/search-what-is-azure-search) is the Azure search-as-a-service solution that allows you to index text documents and is based on Apache Lucene.

- The [fuzzy search capability](https://docs.microsoft.com/en-us/azure/search/search-query-fuzzy) in Azure Cognitive search uses an implementation of [Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) distance to find approximate string matches.
- Using the fuzzy search capability in Cognitive Search, you can [ingest the data sources](https://docs.microsoft.com/en-us/azure/search/search-what-is-data-import) you want to fuzzy match against, index the fields you want to match or de-duplicate on and return the [search scores](https://docs.microsoft.com/en-us/azure/search/index-similarity-and-scoring). You can then use theses scores to set a threshold for what you will consider a match or duplicate.
- The [Knowledge Mining repo](https://github.com/microsoft/dstoolkit-km-solution-accelerator) contains delivery guidance and architecture best practices for using Cognitive Search.

---

## Open Source Solutions (OSS)

A number of OSS libraries exist for fuzzy matching in python, R, and other languages. Below is a table with the algorithms linked to details about how they work, the type of technique, and a Python library where they can be used.

**To be completed**

## Similarity Algorithms for Other Field Types

Similarity algorithms can also be used on numeric, geographic, and date fields. Below is a table that details these.

**To be completed**

## Pre-Processing Techniques

Prior to executing a matching algorithms, it is often useful to use pre-processing techniques to remove things like punctuation, accents, numbers and stop words from strings to improve matching performance. Below are some of the common NLP pre-processing techniques that can be used:

**To be completed**

---

## Scalability Considerations

Because fuzzy matching algorithms typically require every string to be compared to every other string in the dataset, the standard algorithms will have quadratic time complexity O(n^2^) and will quickly become computationally intensive even for modestly large datasets. Two approaches to address the scalability include blocking techniques, which reduce the number of entities that need to be compared, or parallelization techniques including multi-threading and Spark-based approaches which execute multiple matches in parallel.

## Blocking/Indexing Techniques

One method reduce the computational intensity is to restrict the number of records being compared to only plausible pairs. This can be done by restricting comparisons to just records where one or more other factors agree. For example: if you are trying to find all of the records for "John Smith" in New York City, you might only want to compare the name "John Smith" with other customers that have a city listed as New York, rather than the full data set. A method that builds off of this takes not just the records that agree on those factors, but also their nearest neighbors to avoid matching on the full dataset but reduce the number of false negatives from standard blocking. [Python Record Linkage Toolkit](https://recordlinkage.readthedocs.io/en/latest/ref-index.html#recordlinkage.index.Block) has implementations of both of these blocking methods.

Another technique that can be used when a labelled training set is provided is to start with an efficient nearest neighbors search using techniques such as LSH to find the k nearest neighbors for each record and use that to reduce the search space for fuzzy matching. Spotify's [Annoy](https://github.com/spotify/annoy) is one of the most efficient k-NN algorithms which can be used.

## Spark-based Fuzzy Matching

In addition to reducing the search space, scalability concerns can be addressed through Spark-based algorithms for fuzzy matching. [Spark-stringmetric}(https://github.com/MrPowers/spark-stringmetric) has Spark-based implementations of several popular fuzzy matching algorithms including Cosine Distance, Hamming Distance, Jaccard Similarity, and Jaro-Winkler. Twitter's [DIMSUM](https://blog.twitter.com/engineering/en_us/a/2014/all-pairs-similarity-via-dimsum.html) algorithm is a state-of-the-art, highly efficient, token-based algorithm implemented in Spark that is now part of the official MLLib library [columnSimilarities](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html?highlight=rowmatrix#pyspark.mllib.linalg.distributed.IndexedRowMatrix.columnSimilarities) function .

## Parallelization with Dask

[Dask](https://docs.dask.org/en/latest/) is a Python library for parallelizing and optimizing python code to make it scalable. An advantage compared to Spark is that Dask can be used to scale any python code and is therefore not just limited to libraries written for Spark. Rather than operated in a single-threaded manner, Dask allows for multi-threading, scaling across multiple nodes, and lazy execution, similar to Spark. An example of using Dask to parallelize fuzzy matching code using the Python library [fuzzywuzzy](https://github.com/seatgeek/fuzzywuzzy) can be found here in the *notebook* folder.

---

## Machine Learning Approaches

Supervised machine learning can be used when a labelled training set of record matches are provided and provides the advantage of allowing multiple different matching algorithms to be evaluated and weighted based on the performance of the model. In this approach, fuzzy matching becomes a binary classification model where the features of the model are derived from applying multiple fuzzy matching algorithms to each set of records. Feature selection or models which include feature selection can then be applied to select the most useful matching features and then a model can be built on the training data. This model can then be used to predict whether any two strings in the remaining dataset are a match and the model can be evaluated using performance metrics such as precision, recall, F1, or AUC. Further details for this approach can be found in the deck *Deduplication Customer PPT* in the *docs* folder.

In this MLADS talk: [Data Unification at Scale Recording](https://msit.microsoftstream.com/video/2425a1ff-0400-a936-fd76-f1eaa68afd5c?channelId=e34ba1ff-0400-a936-7258-f1eaa6716ffe) the CSEO team discuss their design for design for fuzzy matching for data management that will be part of an upcoming internal tool called Microsoft Data Unification (MDU). Their solution uses blocking and a supervised machine learning approach on Spark. A copy of the deck can be found in the *docs* folder (Data Unification at Scale.PPT).

---

## UX Design Considerations

For entity linkage and de-duplication, one important consideration is whether the process should be fully automated or involve a human-in-the-loop. There are two human-in-the-loop scenarios:

1. It is often appropriate to involve a human in the loop for lower confidence matches to reduce the number of false positives or false negatives, particularly when the consequences are high (for example: social security records).
2. Another UX consideration is regarding labelling, where UX can be used along with fuzzy matching approaches to reduce the number of examples that need to be labelled for a training dataset if you plan to use a supervised machine learning approach. Microsoft Data Unification (MDU's) approach uses this.



