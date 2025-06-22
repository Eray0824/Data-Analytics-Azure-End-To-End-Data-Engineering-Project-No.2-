# Tokyo Olympics Azure Data Engineering Pipeline: An End-to-End Solution

## Overview and Architecture

<!-- Placeholder for your architecture diagram image. Replace with your actual image URL -->
<!-- Example: ![Tokyo Olympics Architecture](https://github.com/your-username/your-repo/assets/your-image-id/your-image-filename.png ) -->
![project 2](https://github.com/user-attachments/assets/15e489cf-0e24-4a2f-992d-b2edeb8b3c16)



### Business Case

The Tokyo Olympic Games generate an immense amount of data, from athlete performance to medal counts. This data is incredibly valuable for sports analysts, federations, and media to gain insights into performance trends, national achievements, and event statistics. An automated data pipeline ensures that these stakeholders can access clean, structured data in a timely manner for informed analysis and reporting, supporting strategic decisions and comprehensive post-event reviews.

### Architecture Overview

This project implements a robust, end-to-end data engineering pipeline on Microsoft Azure, leveraging key services for data ingestion, storage, transformation, and analysis. The architecture is designed for efficiency and scalability, handling various Olympic datasets.

1.  **Data Ingestion**: Azure Data Factory (ADF) is used to orchestrate the daily ingestion of raw Olympic data from a GitHub repository.
2.  **Data Storage**: Azure Data Lake Storage (ADLS) Gen2 serves as the central repository for both raw and transformed data, providing a scalable and cost-effective solution.
3.  **Data Processing**: Azure Databricks, with its Apache Spark capabilities, performs data cleaning, transformation, and preparation.
4.  **Data Analysis**: Azure Synapse Analytics enables powerful SQL querying on the processed data to extract insights.
5.  **Optional Visualization**: The prepared data can be further utilized by tools like Power BI, Looker Studio, or Tableau for interactive dashboards.

### Data Modeling

A layered approach ensures data quality and usability:

1.  **Bronze Layer**: Raw data is ingested directly from the source (GitHub CSVs) into a dedicated `raw_data` folder in ADLS Gen2, maintaining its original format.
2.  **Silver Layer**: Data is cleaned, type-converted (e.g., converting string numbers to integers), and subjected to basic transformations within Azure Databricks. This cleaned data is then stored in a `transformed_data` folder in ADLS Gen2, ready for analytics.

### Understanding the Data Source

The project utilizes the Tokyo Olympics 2021 dataset, originally from Kaggle, comprising several CSV files:
-   `athletes.csv`: Contains athlete names, countries, and disciplines.
-   `coaches.csv`: Details coach names, countries, disciplines, and events.
-   `entries_gender.csv`: Provides gender-specific participant counts per discipline.
-   `medals.csv`: Lists medal counts (gold, silver, bronze, total) and ranks by country.
-   `teams.csv`: Information on teams, disciplines, countries, and events.
For ease of access, these files are sourced from a public GitHub repository.

### Key Benefits

-   **Automated ETL**: Establishes an automated pipeline for data extraction, transformation, and loading, reducing manual effort.
-   **Cloud-Native Scalability**: Leverages Azure's scalable services to efficiently process growing data volumes.
-   **Actionable Insights**: Provides a structured and clean dataset enabling quick and effective analysis for sports insights.
-   **Skill Demonstration**: Showcases proficiency in key Azure Data Engineering services (ADF, ADLS, Databricks, Synapse).

---

## Step-by-Step Implementation

### Step 1: Azure Data Lake Storage Gen2 Setup
1.  A storage account with **hierarchical namespace** enabled was created.
2.  Two containers were created: `raw_data` and `transformed_data` within the storage account.

---

### Step 2: Azure Data Factory Configuration
1.  An Azure Data Factory instance was provisioned.
2.  A pipeline (`data_ingestion`) for data copying was created.
3.  Linked Services were configured:
    -   HTTP Linked Service to connect to the GitHub repository (source).
    -   Azure Data Lake Storage Gen2 Linked Service to connect to the created storage account (destination).
4.  "Copy Data" activities were used to ingest each CSV file from GitHub into the `raw_data` container in ADLS Gen2.

---

### Step 3: Azure Databricks Workspace Setup
1.  An Azure Databricks Workspace was provisioned.
2.  A single-node compute cluster was configured for processing.
3.  Secure connectivity to ADLS Gen2 was established:
    -   An App Registration in Azure Active Directory was created to obtain Client ID, Tenant ID, and Secret Key.
    -   The "Storage Blob Data Contributor" role was assigned to the App Registration for the ADLS Gen2 container.
    -   The ADLS Gen2 container was mounted in Databricks using the obtained credentials, enabling seamless data access.

---

### Step 4: Data Transformation with PySpark in Databricks
1.  PySpark notebooks were developed to read data from the `raw_data` container.
2.  Data quality checks and transformations were performed, including:
    -   Schema inference and header handling when reading CSV files.
    -   Type casting of data (e.g., converting string columns like 'female', 'male', 'total' in `entries_gender.csv` to integer type).
    -   Basic analytical operations were demonstrated (e.g., finding top countries by gold medals, calculating average entries by gender per discipline).
3.  The transformed data was written back to the `transformed_data` container in ADLS Gen2, ensuring proper partitioning (e.g., outputting a single file).

---

### Step 5: Azure Synapse Analytics Integration
<!-- Placeholder for your Synapse screenshot. Replace with your actual image URL -->
<!-- Example: ![Azure Synapse](https://github.com/your-username/your-repo/assets/your-image-id/your-synapse-screenshot.png ) -->
![Azure Synapse Placeholder](https://via.placeholder.com/800x400?text=Azure+Synapse+Screenshot )

1.  An Azure Synapse Analytics Workspace was created and linked to the existing ADLS Gen2.
2.  Serverless SQL Pools were used to query the transformed Parquet/CSV files directly from the `transformed_data` container.
3.  Demonstrated how to extract insights using SQL queries on the prepared data.
    ```sql
    -- Example query for medals data (adapt as needed for your specific analysis)
    SELECT
        country,
        SUM(gold) AS total_gold_medals,
        SUM(silver) AS total_silver_medals,
        SUM(bronze) AS total_bronze_medals,
        SUM(total) AS overall_total_medals
    FROM
        OPENROWSET(
            BULK 'https://<your_storage_account_name>.dfs.core.windows.net/transformed_data/medals.csv',
            FORMAT = 'CSV',
            PARSER_VERSION = '2.0',
            HEADER_ROW = TRUE
         ) AS [result]
    GROUP BY
        country
    ORDER BY
        total_gold_medals DESC;
    ```

---

## Key Considerations
-   **Linked Services**: Ensure reusable and secure connections between Azure services.
-   **Scalability**: Leverage Azure's cloud-native services for efficient handling of large datasets.
-   **Data Engineering Focus**: Maintain an emphasis on structured pipelines and optimized workflows.

This guide provides a comprehensive approach to setting up a professional-grade Azure Databricks and Synapse workflow for data engineering.
