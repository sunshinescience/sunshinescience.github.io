---
layout: post
title: "A Quick Introduction to Python's pandas library"
---

<p align="center"><img src="/assets/img/freestock_55684843.jpg" width="400" height="350"></p>

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
Series is a one-dimensional labeled array that can hold any data type. Series acts similarly to a ndarray and is a valid argument to most NumPy functions. The syntax to create a [Series](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.html) is `s = pd.Series(data, index)` (see example code below). 

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
A DataFrame is a 2-D labeled data structure that can have columns, and each column has only one type. Similar to Series, DataFrame accepts many different kinds of input, such as:
-    Dict of 1D ndarrays, lists, dicts, or Series
-    2-D numpy.ndarray
-    Structured or record ndarray
-    A Series
-    Another DataFrame

Along with the data, you can optionally pass index (row labels) and columns (column labels) arguments.

The syntax to create a DataFrame is: `df = pd.DataFrame(data, index, columns)`. Along with the data, you can optionally pass index (row labels) and columns (column labels) arguments. See example below:

    df = pd.DataFrame([[1,2,3], [4,4,7], [5,8,9]], index=['a', 'b', 'c'], columns=['A', 'B', 'C'])
    df
    
<img src="/assets/img/DataFrame_example.png" width="350" height="200">

## Reading and writing data

## Viewing and assessing data
In the following examples, much of the functionality common to pandas data structures will be applied to DataFrame, but it can also be applied to Series.

## Indexing/selecting data

## Cleaning data
The handling of [missing data](https://pandas.pydata.org/pandas-docs/stable/user_guide/missing_data.html) can be accomplished using 

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

<img src="/assets/img/pandas_join1.png" width="350" height="250">

Next, use `merge()` to join the two DataFrames together.

    pd.merge(left, right, left_index=True, right_index=True)

<img src="/assets/img/pandas_join2.png" width="350" height="300">

The `append()` function can be used to add a row to the end of a DataFrame, which creates a new DataFrame.
Append:
Append a row to a pandas DataFrame
  s = df.iloc[2].astype(int)
  df.append(s)

<img src="/assets/img/pandas_join3.png" width="350" height="300">                                                 

.  .  .


This post barely touches the surface of all of the amazing capabilities of the pandas library. If you liked this tutorial, please have a look at my example on cleaning and formatting near star catalog data using pandas, which can be found [here](https://sunshinescience.github.io/2019/06/30/pandas-example1_stars.html). 

For the pandas documentation, please see [here](https://pandas.pydata.org/pandas-docs/stable/). For some advanced examples using pandas, please see the [cookbook](https://pandas.pydata.org/pandas-docs/stable/user_guide/cookbook.html.). 
    