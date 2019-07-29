---
layout: post
title: "A Quick Introduction to Python's pandas library"
---

<p align="center"><img src="/assets/img/freestock_55684843.jpg" width="400" height="400"></p>

Photo from [https://www.freestock.com/free-photos/3d-business-person-desk-laptop-isolated-55684843](https://www.freestock.com/free-photos/3d-business-person-desk-laptop-isolated-55684843)

The [pandas library](https://pandas.pydata.org/pandas-docs/stable/) is built on NumPy and it provides data analysis tools for Python. Pandas is one of the most widely used tools in data munging/wrangling. The pandas library [offers](https://en.wikipedia.org/wiki/Pandas_(software)) data structures and operations for working with numerical tables as well as time series. Some of the pandas library features include:
-    Data structures (e.g., Series, DataFrame) that store a collection of related data
-    Tools for reading and writing data
-    Viewing and assessing data
-    Indexing/selecting data
-    Cleaning data
-    Grouping data
-    Data set merging/joining
 
## Installation
One way to get pandas is via conda.

    conda install pandas

Full pandas installation instructions can be found [here](https://pandas.pydata.org/pandas-docs/stable/install.html?source=post_page---------------------------). Import the pandas library as:

    import pandas as pd

Once the library, along with any others needed (e.g., [NumPy](https://numpy.org/), [Matplotlib](https://matplotlib.org/)), has been loaded into the memory, it is there to work with. In order to use it, just type a command that is needed as `pd.command`.

## Data structures
A [data structure](https://en.wikipedia.org/wiki/Data_structure) is a format that enables the organization, management, and storage of data. See [here](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html) for an introduction to pandas data structures. Two will be  discussed here, which are Series and DataFrame.

### Series
[Series](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.html) is a one-dimensional labeled array that can hold any data type. Series acts similarly to a ndarray and is a valid argument to most NumPy functions. The syntax to create a Series is `s = pd.Series(data, index)` (see example code below). 

    # Data can be passed as list
    s = pd.Series([1,2,-1,4], 
     index=['a','b','c','d'])
    s

The passed index will set the index for reference (see image below), and it is a list of axis labels.

<img src="/assets/img/Series_example.png" width="200" height="150">

Data in Series can also be one of the following:
-   A Python dict
-   An ndarray
-   A scalar value (e.g., 5)

### DataFrame
A [DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) is a 2-D labeled data structure that can have columns, and each column has only one type. Similar to Series, DataFrame accepts many different kinds of input, such as:
-    Dict of 1D ndarrays, lists, dicts, or Series
-    2-D numpy.ndarray
-    Structured or record ndarray
-    A Series
-    Another DataFrame

Along with the data, you can optionally pass index (row labels) and columns (column labels) arguments.

The syntax to create a DataFrame is: `df = pd.DataFrame(data, index, columns)`. Along with the data, you can optionally pass index (row labels) and columns (column labels) arguments. See example below:

    df = pd.DataFrame([[1,2,3], [4,4,7], [5,8,9]], index=['a', 'b', 'c'], columns=['A', 'B', 'C'])
    
<img src="/assets/img/DataFrame_example.png" width="350" height="200">

## Reading and writing data

## Viewing and assessing data
In the following examples, much of the [functionality](https://pandas.pydata.org/pandas-docs/stable/getting_started/basics.html#basics) common to pandas data structures will be applied to DataFrame, but it can also be applied to Series. To view the DataFrame, run the name of the DataFrame: 

    df

<img src="/assets/img/pandas_viewing1.png" width="300" height="200">

The top *n* rows of your DataFrame can be obtained using df.head(*n*), whereas the bottom rows can be viewed using df.tail(*n*). The index and columns can be accessed using `df.index()` and `df.columns()`, respectively. A NumPy representation of the data can be conducted using `df.to_numpy()`. Data can be transposed using [`df.T`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.T.html). A summary of the descriptive statistics can be derived using[`df.describe()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.describe.html). 

    df.describe().astype(int)
    df

<img src="/assets/img/pandas_describe.png" width="250" height="350">   

A reference summary of some of the common functions (found [here](https://pandas.pydata.org/pandas-docs/stable/getting_started/basics.html)) is as follows:

-   `count` 	Number of non-NA observations
-   `sum` 	    Sum of values
-   `mean` 	    Mean of values
-   `median` 	Arithmetic median of values
-   `min` 	    Minimum
-   `max` 	    Maximum
-   `mode` 	    Mode
-   `abs` 	    Absolute Value
-   `std` 	    Bessel-corrected sample standard deviation
-   `cumsum` 	Cumulative sum

## Indexing/selecting data
Pandas has three ways to [index and select](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html) data, which include: `[ ]`, `.loc`, and `.iloc`. These can accept a callable as indexer. 

**.loc** is mainly label based, but can be used with a boolean array. Inputs include the following:
-    A single label
-    A list or array of labels
-    A slice object with labels 
-    A boolean array
-    A callable, see [selection by callable](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#indexing-callable)

Some examples of how to index using `.loc` are as follows:

    # Get row by label:
    df.loc['b'[0]]

    # Get multi-axis by label:
    df.loc[:, ['A', 'B']]

    # Slicing with the label:
    df.loc['b':'c', ['A', 'B']]

    # Get a scalar value:
    df.loc['a'[0], 'A']
  
**.iloc** is mainly integer position based, but can be used with a boolean array and the inputs include:
  
-    An integer 
-    A list or array of integers 
-    A slice object with ints 
-    A boolean array
-    A callable

Some examples of how to index using `.iloc` are as follows:

    # Select via a passed integer:
    df.iloc[2]

    # Slice integers:
    df.iloc[2:3, 0:3]

    # Use lists of integer position locations:
    df.iloc[[1, 2], [0, 2]]

    # Slice rows:
    df.iloc[1:3, :]

    # Slice columns:
    df.iloc[:, 0:2]

    # Get specific value:
    df.iloc[1,2]
  
Square brackets **[ ]** is the most basic way to index. Example code to use square brackets, is as follows:

    #Select a column:
    df['A']

    #Select by label (i.e., rows):
    df[0:2] 

For example, selecting by label (see code directly above) can provide the following DataFrame.

<img src="/assets/img/pandas_indexing1.png" width="300" height="250">

## Cleaning data
Cleaning data is useful and often necessary in conducting analytics. Note that pandas generally uses the value `np.nan` to represent missing data. The handling of [missing data](https://pandas.pydata.org/pandas-docs/stable/user_guide/missing_data.html) can be accomplished via detecting and filling NaN values. Reindexing can change/add/delete the index on a specified axis and it returns a copy of the data:
    
    df1 = df.reindex(index=['a', 'b','c', 'd'], columns=['A', 'B', 'C', 'D'])
    df1.loc['a':'c', 'D'] = 1 # Setting the value 1 to the first three labels in column D
    df1 

<img src="/assets/img/pandas_missing1.png" width="350" height="300">

One can obtain which rows and/or columns have NaN values.

    df1.isna().any(axis=0) # Get the columns with NaN
    df1[df1.isna().any(axis=1)] # Get the rows with NaN. See figure below.

<img src="/assets/img/pandas_missing4.png" width="350" height="200">

Drop rows with missing data:

    df1 = df1.dropna(how='any').astype(int) 

<img src="/assets/img/pandas_missing5.png" width="350" height="300">

Next, instead of having the number 1 in column 'D', we shall have a NaN value, in order to show how to replace NaN's with a mean value:

<img src="/assets/img/pandas_missing6.png" width="350" height="300">

The operation to determine the mean usually skips NaN values, although one can set `skipna = True` if needed. The mean from all columns can be obtained here via `df1.mean(axis=0)`. An attribute can be used in order to only get the mean for column 'D', such as: `df1.mean(axis=0).D`. 

    # Use an attribute, to get only the column 'D' mean value in this case:
    fill_value = df1.mean(axis = 0, skipna = True).D
    # Filling missing data with the mean from column 'D'
    df1.fillna(value=fill_value).astype(int) 

Column 'D' in this case has a mean of 1 and NaN values have been filled using [df.fillna()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.fillna.html).

<img src="/assets/img/pandas_missing7.png" width="350" height="300">

Values in a DataFrame can also be replaced using [`df.replace()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.replace.html). For example, the number 1 will be replaced with the number 2 in this DataFrame:

    df1.replace(1, 2)

<img src="/assets/img/pandas_missing8.png" width="350" height="300">

## Grouping data
Data can be grouped using [`groupby()`](https://pandas.pydata.org/pandas-docs/stable/user_guide/groupby.html). The documentation refers to a process involving one or more of the following steps:

-    Split data into groups based on a criteria
-    Apply a function to each individual group 
-    Combine the results into a data structure

    # Setting a new column to use in order to group
    df['D'] = ['one','two','one']
    df

<img src="/assets/img/pandas_groupby1.png" width="350" height="300">

As an example, we'll apply `sum()` to each of the groups and combine them using `groupby()`:

    df.groupby('D').sum()

<img src="/assets/img/pandas_groupby2.png" width="350" height="300">

## Data set merging/joining
Pandas also provides ways to combine Series or DataFrames with different kinds of set logic for the indexes. There are three [ways](https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html) to accomplish this, via merge, join, concatenate. 

The [`concat()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.concat.html#pandas.concat) function performs concatenation operations along an axis. Let's first break up our example DataFrame into pieces and then concatinate it back together.

    pieces = [df[0:2], df[2:3]] # Break up the DataFrame into pieces
    pd.concat(pieces) # Concatinate pandas objects together

The `merge()` function merges and it can join columns or indexes. Let's first split the DataFrame into two, for example:

    left = df.iloc[:, 0:2]
    right = df.iloc[:, 2:4]

<img src="/assets/img/pandas_join1.png" width="350" height="200">

Next, use `merge()` to join the two DataFrames together.

    pd.merge(left, right, left_index=True, right_index=True)

<img src="/assets/img/pandas_join2.png" width="350" height="300">

The `append()` function can be used to add a row to the end of a DataFrame, which creates a new DataFrame:
  
    s = df.iloc[2].astype(int)
    df.append(s)

<img src="/assets/img/pandas_join3.png" width="350" height="400">                                                 

.  .  .


This post barely touches the surface of all of the amazing capabilities of the pandas library. If you liked this tutorial, please have a look at my example on cleaning and formatting near star catalog data using pandas, which can be found [here](https://sunshinescience.github.io/2019/06/30/pandas-example1_stars.html). 

For the pandas documentation, please see [here](https://pandas.pydata.org/pandas-docs/stable/). For some advanced examples using pandas, please see the [cookbook](https://pandas.pydata.org/pandas-docs/stable/user_guide/cookbook.html). 
    