---
layout: post
title: "Visualizing near star catalog data from a fixed-width .dat file using pandas"
---

<img src="/assets/img/Sirius_NASA_img.jpg">
Image Credit: [NASA](https://www.nasa.gov/multimedia/imagegallery/image_feature_468.html), ESA, H. Bond (STScI) and M. Barstow (University of Leicester) 
The brightness of a star is dependednt on its size and its distance from Earth. Apparent magnitude is a measure of the relative brightness of a star as seen by an observer. Absolute magnitude is a measure of the luminosity that the star would have if it were viewed from a distance of 10 parsecs (32.6 light-years). Using the absolute magnitude helps to compare luminosities directly on a magnitude scale because the stars are all placed at a standard reference distance from the observer. A method that astronomers use to measure the distance to a star is called parallax. They measure the position of a nearby star with respect to the distant stars behind it, and then take those measurements again six months later (when Earth is on the opposite side of its orbit), and the shift in those distances is the 'prallax.'

The European Space Agency (ESA) released an extensive [map](https://www.esa.int/Our_Activities/Space_Science/Gaia/Gaia_creates_richest_star_map_of_our_Galaxy_and_beyond) of our Milky Way galaxy and stars beyond, based on [data](https://www.cosmos.esa.int/web/gaia/guide-to-scientists) from the ESA’s Gaia mission. Some videos and virtual reality can be found [here](http://sci.esa.int/gaia/60036-gaia-data-release-2-virtual-reality-resources/).

## Near star catalog data set
[Pandas](http://pandas.pydata.org/pandas-docs/version/0.15/tutorials.html) is a Python library used for conducting data analysis. The documentation can be found [here](http://pandas.pydata.org/pandas-docs/stable/user_guide/index.html) and some advanced strategies can be found in the [cookbook](https://github.com/jvns/pandas-cookbook). In this demonstration, a fixed-width file will be read into a pandas DataFrame using [`pd.read_fwf`](https://pandas.pydata.org/pandas-docs/version/0.22/generated/pandas.read_fwf.html). The near star catalog data is from [this]( https://github.com/coucoueric/Python/blob/master/Athena/training/exercises/exercises/python_language/sort_stars/stars.dat) repository. The original data was taken from:
    Preliminary Version of the Third Catalogue of Nearby Stars
    GLIESE W., JAHREISS H.
    Astron. Rechen-Institut, Heidelberg (1991)

It appears that for this data set, the spectral_class column uses the Morgan-Keenan (MK) [system](https://en.wikipedia.org/wiki/Stellar_classification) using the letters O, B, A, F, G, K, and M, a sequence from the hottest (O type) to the coolest (M type). Each letter class is also subdivided using a numeric digit (0 is hottest and 9 is coolest). For simplification, we will use the first letter and the number next to it, from the spectral_class column, in order to plot the data. The absolute magnitude will be plotted against the spectral class, which will be sorted from hottest to coolest.

## Read data from a fixed-width .dat file

Let's read a file and create a Python object with rows and columns, which is called a data frame. A DataFrame is one of the two pandas data containers. A Series is the other, which is a one-dimensional sequence of labeled data. Here, `pd.read_fwf` will be used, however, additional pandas IO tools can be found in the [documentation](http://pandas.pydata.org/pandas-docs/stable/user_guide/io.html).

    import pandas as pd

    widths = [17, 11, 6, 6, 6] # Specifying the width of each column
    names=["name", "spectral_class", "apparent_magnitude", "absolute_magnitude", "parallax"] # Adding names to the header

    # Read a fixed-width file into a DataFrame
    df = pd.read_fwf('stars.dat', colspecs='infer', widths=widths, names=names)
    # Look at the first five rows
    df.head(5)

## Selecting components of the DataFrame

A column can be obtained by indexing into it (i.e., get a subset selection).

    # Get the number of rows and columns
    df.shape

    # Get the individual components of the DataFrame
    index = df.index
    columns = df.columns
    values = df.values

    print (columns)
    print (values)

    # Select two rows and four columns. Note that this can be used for various combinations 
    rows = [0,1]
    cols = ["name", "apparent_magnitude", "absolute_magnitude", "parallax"]
    df.loc[rows, cols]

<img src="/assets/img/Pandas_example_table_1.png">

|       | **name**          | **apparent_magnitude**  | **apparent_magnitude**  |  **parallax**           |
| ----- | -------------:| -------------------:| -------------------:|  -------------:|
| **0** | NN 3001	 |   14.90         |   14.28   |   075.2   |
| **1** | GJ 1001	    |   12.84 |   12.93   |   104.2   |

## Check for missing values
The whole DataFrame can be evaluated for missing values, using `df[.isnull()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.isnull.html).sum().sum()`, however in this case a pandas Series will be made for each column and we will evaluate whether any value is missing in each Series.

    # For example:
    n = df["name"] # Make a Series
    n.isnull().values.any() # Chain .values.any() in order to evaluate whether any value is missing in the Series
    # There are no missing values in this Series

There are 167 missing values in this data set in total. Rows of missing values can be returned using `df[df.isnull().any(axis=1)]`. 

<img src="/assets/img/pandas_missing_values_table2.png">

One missing value is observed in "apparent_magnitude", "absolute_magnitude", and "parallax", which can be found using:

    ap_m_bool = ap_m.isnull()
    df.loc[ap_m_bool == True]

<img src="/assets/img/missing_values_star_text.png">

It appears that the text "The closest star to Earth" is written in the middle of the dataset, which contains no values in the other columns. The function [df.drop()](https://pandas.pydata.org/pandas-docs/version/0.21/generated/pandas.DataFrame.drop.html) can remove that row.

    df.drop([2289])
    # There are now 3802 rows, one row less than before, and row with index 2289 has been removed from the DataFrame

## Determine the statistics
Only row 2289 needed to be removed, so the statisctics can be obtained for the DataFrame by using [.describe()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.describe.html):

    df.drop([2289]).describe(include='all')

<img src="/assets/img/stats_stars_all.png">

## Visualize the data set

