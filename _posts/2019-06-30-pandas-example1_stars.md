---
layout: post
title: "Cleaning and Formatting Near Star Catalog Data using pandas"
---

<p align="center"><img src="/assets/img/Sirius_NASA_img.jpg" width="400" height="350"></p>
Sirius A and B (lower left) photographed via the Hubble Space Telescope.

Image Credit: [NASA](https://www.nasa.gov/multimedia/imagegallery/image_feature_468.html), ESA, H. Bond (STScI) and M. Barstow (University of Leicester) 

The brightness of a star is dependednt on its size and its distance from Earth. Apparent magnitude is a measure of the relative brightness of a star as seen by an observer. Absolute magnitude is a measure of the luminosity that the star would have if it were viewed from a distance of 10 parsecs (32.6 light-years). Using the absolute magnitude helps to compare luminosities directly on a magnitude scale because the stars are all placed at a standard reference distance from the observer. A method that astronomers use to measure the distance to a star is called parallax. They measure the position of a nearby star with respect to the distant stars behind it, and then take those measurements again six months later (when Earth is on the opposite side of its orbit), and the shift in those distances is the 'prallax.'

The European Space Agency (ESA) released an extensive [map](https://www.esa.int/Our_Activities/Space_Science/Gaia/Gaia_creates_richest_star_map_of_our_Galaxy_and_beyond) of our Milky Way galaxy and stars beyond, based on [data](https://www.cosmos.esa.int/web/gaia/guide-to-scientists) from the ESA’s Gaia mission. Some videos and virtual reality can be found [here](http://sci.esa.int/gaia/60036-gaia-data-release-2-virtual-reality-resources/).

In this demonstration, data from nearby stars will be cleaned (e.g., it will be checked for missing values) and formatted using pandas. Statistics will be derived. The absolute magnitude and spectral class data will be visualized. The full code can be found [here](https://github.com/sunshinescience/near_stars/blob/master/near_stars-example.ipynb).

## Near star catalog data set
[Pandas](http://pandas.pydata.org/pandas-docs/version/0.15/tutorials.html) is a Python library used for conducting data analysis. The documentation can be found [here](http://pandas.pydata.org/pandas-docs/stable/user_guide/index.html) and some advanced strategies can be found in the [cookbook](https://github.com/jvns/pandas-cookbook). In this demonstration, a fixed-width file will be read into a pandas DataFrame using [`pd.read_fwf`](https://pandas.pydata.org/pandas-docs/version/0.22/generated/pandas.read_fwf.html). The near star catalog data is from [this]( https://github.com/coucoueric/Python/blob/master/Athena/training/exercises/exercises/python_language/sort_stars/stars.dat) repository. The original data was taken from:
    
 Preliminary Version of the Third Catalogue of Nearby Stars

 GLIESE W., JAHREISS H.
 
 Astron. Rechen-Institut, Heidelberg (1991)

## Read data from a fixed-width .dat file

Let's read a file and create a 2-dimensional Python object with rows and columns, called a DataFrame. A DataFrame is one of the two pandas [data structures](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html). It is a labeled data structure with columns that could have different types. A Series is the other data structure, which is a one-dimensional sequence of labeled data. Here, `pd.read_fwf` will be used, however, additional pandas IO tools can be found in the [documentation](http://pandas.pydata.org/pandas-docs/stable/user_guide/io.html).

    import pandas as pd

    widths = [17, 11, 6, 6, 6] # Specifying the width of each column
    names=["name", "spectral_class", "apparent_magnitude", "absolute_magnitude", "parallax"] # Adding names to the header

    # Read a fixed-width file into a DataFrame
    df = pd.read_fwf('stars.dat', colspecs='infer', widths=widths, names=names)
    
    # Look at the first five rows
    df.head(5)

<img src="/assets/img/stars_5rows.png">


## Selecting components of the DataFrame

A column can be obtained by indexing into it (i.e., get a subset selection).

    # Get the number of rows and columns
    df.shape

    # Get the individual components of the DataFrame
    index = df.index
    columns = df.columns
    values = df.values

    # Select two rows and four columns. Note that this can be used for various combinations 
    rows = [0,1]
    cols = ["name", "apparent_magnitude", "absolute_magnitude", "parallax"]
    df.loc[rows, cols]

|       | **name**          | **apparent_magnitude**  | **absolute_magnitude**  |  **parallax**           |
| ----- | -------------:| -------------------:| -------------------:|  -------------:|
| **0** | NN 3001	 |   14.90         |   14.28   |   075.2   |
| **1** | GJ 1001	    |   12.84 |   12.93   |   104.2   |

## Check for missing values
The whole DataFrame can be evaluated for missing values, using [df.isnull().sum().sum()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.isnull.html), however in this case a pandas Series will be made for each column and we will evaluate whether any value is missing in each Series.

    # For example:
    n = df["name"] # Make a pandas Series for the name column from the DataFrame
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

There are missing values in the spectral_class Series, which were removed in order to plot the values. 

    # Return rows with missing values
    null_data = df[df.isnull().any(axis=1)]
    print (null_data)

    # Get a list of the indexes of the rows with missing values
    index_lst = list(null_data.index.values)

    # Remove several rows with missing values in the spectral_class column
    df.drop(labels=index_lst)

## Determine the statistics
For calculations, only row 2289 needed to be removed, so the statisctics can be obtained for the DataFrame by using [df.describe()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.describe.html):

    df.drop([2289]).describe(include='all')

<img src="/assets/img/stats_stars_all.png">

## Visualize the data set
It appears that for this data set, the spectral_class column uses the Morgan-Keenan (MK) [system](https://en.wikipedia.org/wiki/Stellar_classification) using the letters O, B, A, F, G, K, and M, a sequence from the hottest (O type) to the coolest (M type). These letters are shown on the legend, with their corresponding colors (from red hot to cool blue), in the plot below. Each letter class is also subdivided using a numeric digit (0 is hottest and 9 is coolest). For simplification, we will use the first letter and the number next to it, from the spectral_class column, in order to plot the data. This will be done by approximating the letters as numbers, in the order in which they should appear (see code below). The spectral class of the stars will be sorted from hottest to coolest. The absolute magnitude will be plotted against the spectral class below.

    # Sort stars by temperature via their spectral_class first letter and number

    s_c_letter = df["spectral_class"].str[:1].drop(labels=index_lst).str.upper() # Index into the series "spectral class" to convert the first letter to a corresponding number. Drop missing values in the "spectral class" series. Convert letter to upper.

    s_c_num = df["spectral_class"].str[1:2].drop(labels=index_lst).str.upper() # Second element in the "spectral class" series, which is a number in which 0 is the hottest and 9 is the coolest

    s_c_num_space = df[df["spectral_class"].str[1:2] == ' '].index # Second element in the "spectral class" series that is a space
    s_c_num_dash = df[df["spectral_class"].str[1:2] == '-'].index # Second element in the "spectral class" series that is a dash
    s_c_num_plus = df[df["spectral_class"].str[1:2] == '+'].index # Second element in the "spectral class" series that is a plus

    s_c_num1 = s_c_num.drop(labels=s_c_num_space) # Removing the rows with a space in the second element in the "spectral class" series 
    s_c_num2 = s_c_num1.drop(labels=s_c_num_dash) # Removing the rows with a dash in the second element in the "spectral class" series 
    s_c_num3 = s_c_num2.drop(labels=s_c_num_plus) # Removing the rows with a plus symbol in the second element in the "spectral class" series 

    mapping = {'O': 0, 'B': 1, 'A': 2, 'F': 3, 'G': 4, 'K': 5, 'M': 6} 
    # Letter class in the Morgan-Keenan (MK) system, 0 is the hottest, and 6 is the coolest  
    let = s_c_letter.map(mapping).infer_objects().dropna().astype(int) # changing first letter to number, dropping NaN's, changing the type to int

    let1 = let[let == 1] # Get only value 1 ('B') from the let Series. 
    let2 = let[let == 2] # Get only value 2 ('A') from the let Series.
    let3 = let[let == 3] # Get only value 3 ('F') from the let Series.
    let4 = let[let == 4] # Get only value 4 ('G') from the let Series.
    let5 = let[let == 5] # Get only value 5 ('K') from the let Series.
    let6 = let[let == 6] # Get only value 6 ('M') from the let Series.

    # Sort the spectral class from hottest to coolest
    second_let1 = s_c_num.loc[let1.keys()] 
    second_let2 = s_c_num2.reindex(let2.keys())
    second_let3 = s_c_num2.reindex(let3.keys())
    second_let4 = s_c_num2.reindex(let4.keys())
    second_let5 = s_c_num2.reindex(let5.keys())
    second_let6 = s_c_num3.reindex(let6.keys())

    s_c_second_num1 = pd.to_numeric(second_let1).fillna(value=0).astype(int) 
    s_c_second_num2 = pd.to_numeric(second_let2).fillna(value=0).astype(int)   
    s_c_second_num3 = pd.to_numeric(second_let3).fillna(value=0).astype(int)    
    s_c_second_num4 = pd.to_numeric(second_let4).fillna(value=0).astype(int) 
    s_c_second_num5 = pd.to_numeric(second_let5).fillna(value=0).astype(int) 
    s_c_second_num6 = pd.to_numeric(second_let6).fillna(value=0).astype(int) 

    s_c_sorted1 = (let1.astype(str) + s_c_second_num1.astype(str)).astype(int) # Concatenate first element (letter) of spectral_class series and second element (number) of spectral_class series. Then turn it into an integer. 
    s_c_sorted2 = (let2.astype(str) + s_c_second_num2.astype(str)).astype(int)
    s_c_sorted3 = (let3.astype(str) + s_c_second_num3.astype(str)).astype(int)
    s_c_sorted4 = (let4.astype(str) + s_c_second_num4.astype(str)).astype(int)
    s_c_sorted5 = (let5.astype(str) + s_c_second_num5.astype(str)).astype(int)
    s_c_sorted6 = (let6.astype(str) + s_c_second_num6.astype(str)).astype(int)

    #Note that the temperature values in this plot are not accurate, but only represent the temperature based on the the Morgan-Keenan (MK) system, as simplified here

    # Plot the absolute magnitude vs. the spectral class temperature (from hottest to coolest)
    fig, ax = plt.subplots(figsize=(7,10))
    plt.scatter(s_c_sorted1, df["absolute_magnitude"].loc[keys1], c='red', s=175, label='B')
    plt.scatter(s_c_sorted2, df["absolute_magnitude"].loc[let2.keys()], c='orangered', s=150, alpha=0.5, label='A')
    plt.scatter(s_c_sorted3, df["absolute_magnitude"].loc[let3.keys()], c='darkorange', s=125, alpha=0.5, label='F')
    plt.scatter(s_c_sorted4, df["absolute_magnitude"].loc[let4.keys()], c='orange', s=100, alpha=0.5, label='G')
    plt.scatter(s_c_sorted5, df["absolute_magnitude"].loc[let5.keys()], c='gold', s=75, alpha=0.5, label='K')
    plt.scatter(s_c_sorted6, df["absolute_magnitude"].loc[let6.keys()], c='lightskyblue', s=50, alpha=0.5, label='M')
    plt.xlabel('Hottest to coolest', fontsize=20)
    plt.ylabel('Absolute magnitude', fontsize=20)

    # Remove tick marks form x-axis
    plt.tick_params(
        axis='x',          # Changes apply to the x-axis
        which='both',      # Both major and minor ticks are affected
        bottom=False,      # Ticks along the bottom edge are off
        top=False,         # Ticks along the top edge are off
        labelbottom=False) # Labels along the bottom edge are off

    # Change fontsize of ticks label 
    ax.tick_params(axis='both', which='major', labelsize=14)
    ax.tick_params(axis='both', which='minor', labelsize=8)

    # Add legend
    plt.legend(loc='best')

    # Add arrow to show hottest to coolest direction
    ax.arrow(20, -1.5, 35, 0, head_width=0.5, head_length=5, fc='k', ec='k')
    
    plt.show()

<img src="/assets/img/mag_spectral_class_plot.png">

It appears that there is a trend shown here, in which the absolute magnitude increases with cooler stars (based on their spectral class). Stars are classified based on their spectra (elements they absorb) and their temperature. Stars with lower luminosities emit less light, and thus are dimmer (i.e., they have higher absolute magnitudes). The more luminous a star is, the smaller the numerical value of its [absolute magnitude](https://en.wikipedia.org/wiki/Absolute_magnitude). This is approximately illustrated in the trend shown here.