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
`bigquery-public-data` is a project. And a project is a collection of datasets. `chicago_crime` is a dataset. And a dataset is a collection of tables. In order to get the dataset from BigQuery using Kaggle's BigQuery integration, use the following code as the next step in the workflow:

    # Build a reference to the "chicago_crime" dataset
    dataset_ref = client.dataset("chicago_crime", project="bigquery-public-data")

    # API request - get the dataset
    dataset = client.get_dataset(dataset_ref)

Add data by clicking on the `+Add Data` tab in your Kernel. Type `chicago crime` in the search bar in click `add` to add it to the Kernel. In order to get an idea of what is in the dataset, use the following commands. Let's use the `list_tables()` method to list the tables that are in the dataset.

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

### General code template using Kaggle's public dataset BigQuery integration
Each time when using a BigQuery dataset via Kaggle, the following code will be used - and it will be the same every time. Generally, what will be changed is the specific dataset that you're pointing towards, the name(s) of the tables, and the name(s) of the columns and things that you're interested in.

    from google.cloud import bigquery

    # Create a "Client" object
    client = bigquery.Client()

    # Construct a reference to the "chicago_crime" dataset
    dataset_ref = client.dataset("chicago_crime", project="bigquery-public-data")

    # API request - get the dataset
    dataset = client.get_dataset(dataset_ref)

    # Construct a reference to the "crime" table
    table_ref = dataset_ref.table("crime")

    # API request - fetch the table
    table = client.get_table(table_ref)

    # Preview the first five lines of the "crime" table
    client.list_rows(table, max_results=5).to_dataframe()

Be sure to add the dataset to the kernel you're working with.

### SQL Statements

The code of SQL that you send to a database to get some information back is called a query. And queries are made up of a variety of clauses.  And the clauses are based on keywords, such as SELECT, FROM, WHERE, etc. The SQL that we write here will be in text strings, and then we'll use a Python wrapper to send it to BigQuery and get it back.

In order to avoid going over the limit of scanning 5TB every 30 days for free via Kaggle, they have shown us how to see how much data a query will scan, see [here](https://www.kaggle.com/dansbecker/select-from-where). First, estimate the size of a query before running it. To see how much data a query will scan, create a `QueryJobConfig` object and then set the `dry_run` parameter to `True`. For example:

    # You can get an estimate of the size of your query without running it, by using code such as:

    # Query to get the date and description column from every row where the type column has value "ROBBERY"
    query_example = """
            SELECT date, description 
            FROM `bigquery-public-data.chicago_crime.crime` 
            WHERE primary_type = 'ROBBERY'
            """
            
    # Create a QueryJobConfig object in order to estimate the size of the query without running it
    dry_run_config = bigquery.QueryJobConfig(dry_run=True)

    # API request - dry run query  
    dry_run_query_job = client.query(query2, job_config=dry_run_config)
    n_bytes = dry_run_query_job.total_bytes_processed
    n_mega_bytes = n_bytes/1e+6
    print("This query will process {:.0f} megabytes.".format(n_mega_bytes))

<img src="/assets/img/sql_big_query_chic_crime_query_sz.png">

It's possible to set something up to run a query if its below a specified size, or if its above then you can instead get an error message:

    # Change the maximum_bytes_billed (see code below) to ONE_GB if you need a larger qeury run
    ONE_GB = 1000*1000*1000

    # Only run the query if it's < 1 MB
    ONE_MB = 1000*1000
    safe_config = bigquery.QueryJobConfig(maximum_bytes_billed=ONE_MB)

    # Set up the query (will only run if it's < 1 MB)
    safe_query_job = client.query(query2, job_config=safe_config)

    # API request - try to run the query, and return a pandas DataFrame
    safe_query_job.to_dataframe()

#### SELECT, FROM, WHERE
We'll next be using some SQL keywords, which include SELECT, FROM, and WHERE in order to get data from specific columns based on specified conditions. The SELECT statement is used to select data from a database, the FROM statement is used to specify the table (or database), and the WHERE is used for what row quality you're interested in. 

To write a SQL query that selects a single column from a single table, write the following:

-    after the word SELECT specify the *column*
-    after the word FROM specify the *table* 

For example, let's send something to BigQuery as a text string:

    query = """
            SELECT location_description 
            FROM `bigquery-public-data.chicago_crime.crime` 
            WHERE primary_type = 'ROBBERY'
            """

    # Use the client to send the query to BigQuery
    query_job = client.query(query)

    # To get the information out of it, get it in the form of a dataframe:
    robberies = query_job.to_dataframe() # This is now a Pandas DataFrame that you can use Pandas commands with
    print (robberies)

<img src="/assets/img/sql_big_query_chic_crime_df_robberies.png">

Note that if you wanted more columns, just put a column after each column name in this example (right after the `SELECT` is the `location_description` column, and you'd just add a comma to get more columns). For example, if we want to pull out data from the columns location_description, date, and location, the query would be:

    query = """
            SELECT location_description, date, location 
            FROM `bigquery-public-data.chicago_crime.crime` 
            WHERE primary_type = 'ROBBERY'
            """

You can use `SELECT *` (see code below) in order to select all columns, but use caution as you've got a limited amount that you can pull every month and some BigQuery datasets are huge.

    query = """
            SELECT *
            FROM `bigquery-public-data.chicago_crime.crime` 
            WHERE primary_type = 'ROBBERY'
            """

The code below is a reference to a DataFrame object, (the DataFrame was previously made and called 'robberies'). We're looking at the 'location_description' column. We'll use `.value_counts()` to count the values and just print out the first five rows using `.head()`:

    robberies.location_description.value_counts().head()

<img src="/assets/img/sql_big_query_chic_crime_val_counts.png">

There are 81089 measurements from robberies on the street, 81050 robberies on the sidewalk, 18595 robberies in an alley and so on.

You can get an estimate of the size of your query without running it, by doing the following:

    # Query to get the date and description column from every row where the type column has value "ROBBERY"
    query2 = """
            SELECT location_description
            FROM `bigquery-public-data.chicago_crime.crime` 
            WHERE primary_type = 'ROBBERY'
            """
            
    # Create a QueryJobConfig object in order to estimate the size of the query without running it
    dry_run_config = bigquery.QueryJobConfig(dry_run=True)

    # API request - dry run query  
    dry_run_query_job = client.query(query2, job_config=dry_run_config)
    n_bytes = dry_run_query_job.total_bytes_processed
    n_mega_bytes = n_bytes/1e+6
    print("This query will process {:.0f} megabytes.".format(n_mega_bytes))

<img src="/assets/img/sql_big_query_chic_crime_query_sz.png">

    # Only run the query if it's less than 1 MB
    ONE_MB = 1000*1000
    safe_config = bigquery.QueryJobConfig(maximum_bytes_billed=ONE_MB)

    # Set up the query (will only run if it's < 1 MB)
    safe_query_job = client.query(query2, job_config=safe_config)

    # API request - try to run the query, and return a pandas DataFrame
    safe_query_job.to_dataframe()

#### GROUP BY, HAVING, COUNT
`COUNT()` is one example of an aggregate function, which takes several values and returns one value. The `GROUP BY` statement can be used with aggregate functions (`COUNT`, `MAX`, `MIN`, `SUM`, `AVG`) to *group* the result set by one (or more) column(s). `HAVING` filters things once they've been grouped using `GROUP BY`. `HAVING` is used in combination with `GROUP BY` to ignore groups that don't meet a certain criteria. `HAVING` is similar to `WHERE`, but it is used in conjunction with `GROUP BY`. See the code below for an example.

    # Let's see how many community areas had arrests greater than 10 and let's also count the unique keys

    # Making a text string with a SQL query in it:
    query3 = """
            SELECT community_area, COUNT(unique_key)
            FROM `bigquery-public-data.chicago_crime.crime` 
            GROUP BY community_area
            HAVING COUNT(arrest) > 10
            """
    
    # Give the client the above text string
    query_job3 = client.query(query3)

    # The client takes it to BiqQuery and then BigQuery does the parsing of the SQL query here
    arrests = query_job3.to_dataframe() # This is now a Pandas DataFrame that you can use Pandas commands with
    arrests.head()

<img src="/assets/img/sql_big_query_chic_crime_group_by_count.png" width="250" height="150">

Note that using `COUNT(1)`, in the above text string, would count the number of rows in each group. The column resulting from COUNT(unique_key) was given the name `f0__`. The name can be changed by adding `AS name` after specifying the aggregation, which is called aliasing:

<img src="/assets/img/sql_big_query_chic_crime_unique_key.png" width="250" height="150">

Note that your results will not have the exact same order each time you run the code using BigQuery.

#### ORDER BY
`ORDER BY` is a way to sort things in your returned query. Its usually the last clause in your query. You may want to sort using this because SQL is faster than other languages (e.g., Python) and you can just order things quicker this way. This clause works for columns containing numbers or columns that contain text. For example, let's order robberies by year in this dataset:

    # Use ORDER BY to sort the year in the query
    query4 = """
            SELECT location_description, arrest, year  
            FROM `bigquery-public-data.chicago_crime.crime` 
            WHERE primary_type = 'ROBBERY' 
            ORDER BY year
            """
    # Give the client the above text string called query4
    query_job4 = client.query(query4)

    # The client takes it to BiqQuery 
    arrests = query_job4.to_dataframe() # This is now a Pandas DataFrame 
    arrests.head()

<img src="/assets/img/sql_big_query_chic_crime_order_by.png " width="250" height="150">

The order can be reversed using the `DESC` argument (short for descending):

    query4 = """
            SELECT location_description, arrest, year  
            FROM `bigquery-public-data.chicago_crime.crime` 
            WHERE primary_type = 'ROBBERY' 
            ORDER BY year DESC
            """
            
<img src="/assets/img/sql_big_query_chic_crime_order_by_desc.png" width="250" height="150">


#### Dates
Please see the documentation [here](https://cloud.google.com/bigquery/docs/reference/standard-sql/date_functions) for date functions. The two ways that dates can be stored in BigQuery are as a: DATE or DATETIME. The DATE format has the year first, then the month, and then the day. 

`YYYY-[M]M-[D]D`

-    YYYY: Four-digit year
-    [M]M: One or two digit month
-    [D]D: One or two digit day

The DATETIME format is similar to the date format, but with time at the end.

`EXTRACT` can be used to get the value corresponding to the specified date part.
The syntax is `EXTRACT(part FROM date_expression)`. The part needs to be from one of the parts as found [here](https://cloud.google.com/bigquery/docs/reference/standard-sql/date_functions) (e.g., DAYOFWEEK, DAY, etc).

    query_date = """
            SELECT location_description, EXTRACT(DAY from date) AS day    
            FROM `bigquery-public-data.chicago_crime.crime` 
            WHERE primary_type = 'ROBBERY' 
            """
    # Give the client the above text string called query4
    query_job_date = client.query(query_date)

    # The client takes it to BiqQuery 
    arrests = query_job_date.to_dataframe() # This is now a Pandas DataFrame 
    arrests.head()

<img src="/assets/img/sql_big_query_chic_crime_day.png" width="250" height="150">


<center>Happy coding!<center>

<center>.           .           .<center>