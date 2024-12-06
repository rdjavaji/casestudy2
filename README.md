# Casestudy2

This casestudy demonstrate ingesting data from database server and http API into the datalake storage account. Performing data cleaning and transformations, and storing the results in an Azure Data Lake house and Synapse Analytics.

It involved several key steps, including setting up the environment, creating databases and tables, Performs integration of various Azure data services, SQL Server, HTTP, and Key vaults, Linked services, datasets, 
parameterised pipelines for processing and transforming data. Performing data cleaning and transformations, and storing the results in an Azure Data Lake house and Synapse Analytics and Pull request in GitHub repo.

## SQL Server Setup:

- Installed SQL Server and SQL Server Management Studio (SSMS).
- Established a connection to SSMS and created a new database called "ssmscasestudy."
- Created multiple tables for customers, accounts, transactions, loans, and loan payments to simulate a financial system.
- Inserted sample data into these tables representing customers, their bank accounts, transactions, loans, and loan payments.

[Query] (https://github.com/rdjavaji/casestudy2/blob/main/SQL%20Server%20SSMS/query.sql)

## GitHub Repository and Data Upload (Considering the data for HTTP API):

- Created a GitHub repository and uploaded a file named ecommers containing customer data. This file served as an HTTP source for the data pipeline in Azure.

## Azure Setup:

- created Resource Group. (Under this resource group created Azure data factory, Storage account (Azure Data Lake storage Gen2), Azure Data bricks, Key vault.
- Azure Data Lake Storage: Created a storage account named “mystoragecase2” and created containers named Raw, Silver, and Gold.
- Key vault: Created key vault with name "casestudy2keyvault".
- Azure Data Factory: Created an instance of Azure Data Factory (ADF) called “adf-source-raw-cs2.”
- GitHub: Configured Github branch as "Dev" to Azure Data factory
- Linked Services: Configured linked services to SQL Server, HTTP, and Azure Data Lake.
- Datasets: Created datasets for SQL Server, HTTP, and Azure Data Lake.

## Azure Data Lake Storage:

- Azure Data Lake Storage: A storage account was created with containers named Raw, Silver, and Gold. These containers are used for different stages of data processing
- Raw: Contains unprocessed data.
- Silver: Contains cleaned and transformed data.
- Gold: Contains aggregated and refined data.

## Azure Data Factory 

Azure Data Factory: ADF was used to create data pipelines for transferring data from SQL Server and HTTP (from GitHub) to Azure Data Lake Storage (Raw container).
Linked Services: Linked services were set up to connect ADF to SQL Server, HTTP (for GitHub), and Azure Data Lake Storage.
Datasets: Datasets were created for each data source to define the schema and structure of the data.

## Pipelines Parameterised in Azure Data Factory:

- First Pipeline: Transferred data from SQL Server to Azure Data Lake Storage (Raw container).
- Second Pipeline: Transferred data from the HTTP source (Data from GitHub) to the Raw container in Azure Data Lake.
- Scheduled Trigger for 7 days.
- Data Storage: The Raw container now contained both SQL Server data and the HTTP data from the ecommers file.

## Azure Databricks:

- Azure Databricks was created with name "casestudy2_databricks" and created a cluster, folder, Notebook. 
- Retrieved raw data from Azure Data Lake Storage into Azure Databricks.
- Performed data cleaning and transformations in Databricks, storing the cleaned data in the Silver container of Azure Data Lake.

##### Data Cleaning and Transformation:
- Null Value Removal: Null values were removed from the data using dropna().
- Data Skew and Aggregation: Transactions data was analyzed for skew by grouping data by account_id and summing the transaction amounts. Repartitioning was done to reduce data shuffling.
- Salting Technique: Random "salt" was added to the account_id in order to evenly distribute data across partitions and reduce skew.
- Data Type Conversion: Various columns were cast to the appropriate data types, such as converting customer_id to integers and balance to doubles, ensuring proper data types for analysis.
- Renaming Columns: For instance, the column zip was renamed to postal_code for consistency.
- Joins and Grouping: More complex transformations, such as joining tables and grouping data by account, were performed in Databricks. This step aimed to create a unified, business-friendly dataset.
- The transformed data was then stored in the Gold container, which is intended for final consumption by analytics tools.)

##### Delta Lake (Silver Container):

- After cleaning, data was written back to the Silver container in Delta format, using Delta Lake for version control and ACID transaction support.
- Delta tables were created for accounts, customers, loan_payments, loans, and transactions.

##### Data Aggregation and Joins:

- Data was read from the Silver container, and several aggregation operations (e.g., summing transaction amounts) were performed.
- Data Shuffling with groupBy(), Salting were performed.
- A join operation was executed on the transactions and customers tables using salted keys to minimize skew and ensure better distribution of the data.

##### Saving Final Data (Gold Container):

- After cleaning and aggregating the data, the final result was written to the Gold container, ready for downstream analysis or reporting.

[pyspark] (https://github.com/rdjavaji/casestudy2/blob/main/Azure%20Databricks/Processing-Final.ipynb)

##### Day 2 - Identify Data Skew 

- Performed Group transactions by account_id and calculate the total transaction amount for each customer. As customers have many more transactions than others, this result in a skewed dataset
- transactions_grouped result was written to Gold container, ready for downstream analysis or reporting.

## Azure Synapse Analytics:

- Created an external table in Azure Synapse Analytics to enable querying the processed data. This step allowed users to run SQL queries over the Gold container's data for reporting, analysis, and decision-making.

## GitHub Pull Request: 

- Created New resource group with name: my_rg
- Created New Azure data Factory: pr_adf1
- Configured Github branch as "QA" to Azure Data factory
- Created Pull Request from branch Dev to QA








