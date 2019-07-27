---
layout: post
title: "Quick Introduction to Python's pandas library"
---

<img src="/assets/img/freestock_55684843.jpg" width="300" height="150">
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

<img src="/assets/img/Series_example.png" width="150" height="150">

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
    
<img src="/assets/img/DataFrame_example.png" width="150" height="150">

## Reading and writing data

## Viewing and assessing data

## Indexing/selecting data

## Cleaning data
    The handling of missing data can be accomplished using 

## Grouping data

## Data set merging/joining