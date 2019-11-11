---
layout: post
title: "Using SQL and BigQuery - in progress"
---

### What is SQL?
Structured Query Language (or [SQL](https://en.wikipedia.org/wiki/SQL)), is the programming language used with databases. You use this to interact with a relational database. We'll use SQL to interact with BigQuery datasets, from within Python. A lot of information about SQL can be found in the Microsoft SQL [documentation](https://docs.microsoft.com/en-us/sql/?view=sql-server-ver15). And [here](https://www.w3schools.com/sql/sql_syntax.asp) are some examples of some important SQL commands.

### Getting started
Here, we'll go through the workflow for handling big datasets with BigQuery and SQL, with many thanks to Kaggle's SQL course which can be found [here](https://www.kaggle.com/learn/intro-to-sql). First, let's [create a new kernel](https://www.kaggle.com/getting-started/23393) in Kaggle, so once signed into your Kaggle account, click the Kernels tab and click on Notebook. Enter a title for your notebook. Delete the existing code in the first cell and try printing a test message. Commit the notebook (its similar to saving the file in order to see the output). View the output by running the cell.

It is gernerally recommended that you use the BigQuery Client Library, installation can be found [here](https://cloud.google.com/bigquery/docs/reference/libraries#client-libraries-install-python). In your Kaggle notebook,  import the Python package below:

    from google.cloud import bigquery

The initial step in the workflow is to create a [Client](https://googleapis.dev/python/bigquery/latest/generated/google.cloud.bigquery.client.Client.html) object, which  will play an important role in retrieving information from BigQuery datasets. The Client object will hold the project and a connection to the BigQuery service.

    # Create a 'Client' object
    client = bigquery.Client()

### Obtain the dataset from BigQuery
`bigquery-public-data` is a project. And a project is a collection of datasets. `chicago_crime` is a dataset. And a dataset is a collection of tables. In order to get the dataset from BigQuery in your Kaggle Kernel, use the following code:

    # Build a reference to the "chicago_crime" dataset
    dataset_ref = client.dataset("chicago_crime", project="bigquery-public-data")

    # API request - get the dataset
    dataset = client.get_dataset(dataset_ref)

Add data by clicking on the `+Add Data` tab in your Kernel. Type `hacker news` in the search bar in click `add` to add it to the Kernel. In order to get an idea of what is in the dataset, use the following commands. Let's use the `list_tables()` method to list the tables that are in the dataset.

    # List all the tables in this dataset (there is only one table in this particular dataset)
    tables = list(client.list_tables(dataset))

    # Print the number of tables in the dataset
    print (len(tables))

    # Print out the names of all tables in the dataset  
    for table in tables:  
        print(table.table_id)

Create a reference to a table, since there is only one in this dataset, called crime. Let's fetch the `crime` table in the `chicago_crime` dataset:

    # Construct a reference to the "crime" table
    table_ref = dataset_ref.table("crime")

    # API request - fetch the table
    table = client.get_table(table_ref)

### Table schema
The structure of a table is called its schema. We need to understand a table's schema in order to pull out the data we need. Let's have a look at what information is in the table called `crime`. In the (partial) image below, each SchemaField shown is a column  (which is also referred to as a field). In order, the information printed out is:

-    The name of that column
-    The field type (or datatype) in that column
-    The mode of the column ('NULLABLE' means that a column allows NULL values, and this is the default)
-    A description of the data that is in the column

<img src="/assets/img/sql_big_query_chic_crime_columns.png" width="700" height="500">

In order to look at some of the rows (e.g., the first five rows), use the `list_rows()` method, which is similar to using the .head() method in Pandas.

    # See the first five lines of the "crime" table
    client.list_rows(table, max_results=5).to_dataframe()

A partial image of the output looks something like this:

<img src="/assets/img/sql_big_query_chic_crime_5rows.png" width="700" height="500">





<center>Happy coding!<center>

<center>.           .           .<center>