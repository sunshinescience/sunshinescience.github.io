---
layout: post
title: "Dimensionality Reduction and Clustering on the Handwritten Digits Dataset"
---

<img src="/assets/img/gear_head_concept.jpg" width="500" height="500">

One of the goals of science is the pursuit of knowledge. And one of the goals of technology is to create products that solve problems. As technology appears to be the practical application of science, I think that applying machine learning (ML) techniques is exciting! ML is a ubiquitous technology –it is found everywhere! Just a few applications include the type of product a person might buy, the ability for cars to drive themselves, determining if an email is spam and moving it into your spam folder, and the list goes on…

My interest in machine learning piqued as I learned what an incredible tool it can be for transforming information into knowledge. These techniques provide various ways to solve a problem.  Machine learning is a method of providing systems the ability to learn and improve from experience in an automated fashion, without the need for explicitly being programmed.  Two of the broad categories in which machine learning tasks are classified into are: supervised learning and unsupervised learning. 
- **Unsupervised learning** essentially learns from unlabelled and uncategorized input data. 
- **Supervised learning** undertakes the task of learning a mapping function based on input-output pairs. This task learns the mapping function so well that it maps an input to predict an output. 

I have found scikit-learn incredibly useful. Scikit-learn supports several categories of algorithms, such as regression, classification, clustering, and dimensionality reduction. It offers tools that allow you to find the best model for your data (model selection). The scikit-learn algorithm cheat-sheet can be found [here](https://scikit-learn.org/stable/tutorial/machine_learning_map/index.html). Moreover, scikit-learn has tools for data processing and preparation ([preprocessing](https://scikit-learn.org/stable/modules/preprocessing.html)). 

As a scientist, I really enjoyed finding patterns and structure in various forms of data. An interesting machine learning project to delve into may be clustering, as one of the goals of clustering is to identify structure in data. And dimensionality reduction might improve the clustering of the data. Thus, in this demonstration, dimensionality reduction and subsequent clustering methods will be explored. While I would like to apply this to high dimensional unlabelled data, I would initially like to work with a labelled dataset. First, I'll assess the handwritten digits dataset, as it has been tagged with labels. I'll use Python for this demonstration.

## Dataset
For this demonstration, I’ll use the handwritten digits dataset from scikit-learn. Scikit-learn has a copy of the test set of the UCI ML hand-written digits [dataset](http://archive.ics.uci.edu/ml/datasets/Optical+Recognition+of+Handwritten+Digits). An example to load and visualize the data via scikit-learn can be found [here](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_digits.html). The dataset characteristics on scikit-learn can be found in section [5.2.4](https://scikit-learn.org/stable/datasets/index.html#digits-dataset).

This dataset is made up of 1797 images of hand-written digits. There are ten classes, in which each class refers to a handwritten digit (0 to 9). See an example of digits 0 to 9 in the images below. The axes are the pixel numbers, for each 8x8 pixel image.
<img src="/assets/img/digits_10example.png" width="500" height="250">

Let's first import a few Python libraries that are useful. Let's also import some libraries from scikit-learn and load the dataset.
    
    import numpy as np
    import matplotlib.pyplot as plt

    from sklearn.datasets import load_digits
    from sklearn.preprocessing import scale

    # Load the handwritten digits dataset
    digits = load_digits()

    # Standardize a dataset along any axis 
    data = scale(digits.data) 

The following code will generate the above figure:

    # Plot of ten example digits (from 0 to 9)
    fig = plt.figure(figsize=(10,5))
    plt_index = 0
    for i in range(10):
        plt_index = plt_index + 1
        ax = fig.add_subplot(2, 5, plt_index)
        ax.imshow(digits.images[i], cmap=plt.cm.gray_r, interpolation='nearest')
    plt.tight_layout()
    plt.show() 

Each individual label indicates which digit (0 to 9) is captured in that particular image. When plotting, each individual data point represents an 8x8 image of a digit. Each 8x8 image has been pre-processed such that the digit fits and is centered. Generally if an image is in grayscale, the tuple returned contains only the number of rows and columns, whereas a color image would have a tuple returned of rows, columns, and channels. In this example, these images are grayscaled as the tuple returned is 8x8 using the code: 

    print (digits.images[0].shape)

Generally, the dimensionality in an image is the number of pixels within its image. In this case, each image consists of 64 pixels that represent the features of that particular digit. A feature can be thought of as an individual measureable property of something that is being observed. Thus, instead of thinking of each image as an 8x8 grid of pixels, we can consider it as a vector, or list, of 64 features.

Here, I'll show a plot of four of each of the nine digits in order to illustrate differences contributed by participants whom had written the digits. The ticks and corresponding tick labels have been removed for ease of view. See the plot and corresponding code below.

    # Plot of four examples from each of the 10 digits (0 through 9)
    sample_lst = [] # A list of lists. Each list within contains four sample numbers of a single digit, and it goes from digit 0 (the first list within the list of lists) to digit 9 (the last list)
    for i in range(10):
        sample_nums = np.where(labels == i)[0][0:4] # Indexes (0:4) into an array of sample numbers that are a specified digit (the digit is written as i in the loop)
        sample_nums = sample_nums.tolist() # Convert the array called sample_nums to a list with the same items
        sample_lst.append(sample_nums) 
    if 0:
        fig = plt.figure(figsize=(10,6))
        plt_index = 0
        for i in range(0,10):
            for j in sample_lst[i]:
                plt_index = plt_index + 1
                ax = fig.add_subplot(5, 8, plt_index)
                ax.imshow(digits.images[j], cmap=plt.cm.gray_r, interpolation='nearest')
                ax.set_yticklabels([]) # Turn off y tick labels
                ax.set_xticklabels([]) # Turn off x tick labels
                ax.set_yticks([]) # Turn off y ticks
                ax.set_xticks([]) # Turn off x ticks
        plt.tight_layout()
        plt.show()  

<img src="/assets/img/digits_each_4.png" width="700" height="500">

## Preprocessing
The [sklearn.preprocessing](https://scikit-learn.org/stable/modules/preprocessing.html) package contains numerous functions and classes to change features into a representation that would be useful for learning algorithms. It may be good practice to initially standardize a dataset, as it is usually advantageous to apply learning algorithms to a standardized dataset. If the dataset contains outliers, scalars or transformers might be more beneficial. 

The goal in standardizing and normalizing is to change the values (e.g., numeric feature values within a column) in order to use a common scale, without distorting differences in the ranges of values or losing information. This can help the model overcome learning problems. 
-  Data *normalization* is the process of scaling individual samples to have unit norm (i.e., a vector of length 1). When a feature is normalized, the feature values are in between 0 and 1. 
-  Data *standardization* is the process of transforming features so that each feature looks like standard normally distributed data. And each feature is distributed about zero with a standard deviation of 1. 

The learning algorithms might not work as well if the individual features do not appear similar to standard normally distributed data (i.e., Gaussian with zero mean and unit variance). For example, it is assumed by elements in the objective function of a learning algorithm that all features are centered around zero and have similar variances. But, if a feature has a variance that is much larger than other features, it might dominate the objective function of the learning algorithm and thus make the algorithm unable to learn from other features correctly. Here, the data is standardized in the code above.

## Dimensionality reduction
Dimensionality is the number of features in your dataset. Dimensionality reduction is a process that reduces that number of features. It might be best to try to attain a compact representation of high-dimensional data without having to lose much information. There are many benefits in its applications. One advantage is that it reduces the processing time and storage space needed for the particular modelling algorithm. It may help avoid the effects of the curse of dimensionality. It can also improve the interpretation of the parameters of the machine learning model. Also, in order to visualize data, it needs to be reduced to very low dimensions (2D or 3D). There appears to be two key dimensionality reduction methods:
-  Feature selection – A subset is selected from the original feature set. 
-  Feature extraction - A new (smaller) set of features is created from the original feature set. This new set of features captures the most useful information in the data. 

A guide that explains both feature selection and feature extraction in detail can be found [here](https://elitedatascience.com/dimensionality-reduction-algorithms).

For comparison purposes, the following dimensionality reduction techniques will be used: principal component analysis, t-distributed Stochastic Neighbor Embedding, truncated singular value decomposition, and Isomap. 

### Principal component analysis 
Principal component analysis (PCA) is a technique that reduces dimensionality of a dataset that comprises several interrelated variables, while retaining much of the variation within the dataset. This occurs by transforming to a new set of variables, the principal components (PCs). These PCs are not correlated and they are ordered such that the first few of them retain most of the variation contained within all of the original variables.

The idea of PCA is to express the data in terms of a few well-chosen vectors that more efficiently capture the variation in that data. Generally, the goal is to save space while avoiding redundancies and eliminating the least important information. PCA is one of the most widely used techniques for dimensionality reduction and it is often used with large datasets with many features. 
This technique finds a combination of features that capture the variance of the original features well. Thus, I thought it best to use this technique first in order to have a look at what it does to the dataset. I won’t delve into the mathematics here, but the derivation of the principal components (and much additional information) can be found [here](https://www.springer.com/gb/book/9780387954424).

First, let’s visualize the original data (i.e., this data is not standardized). We’ll project the original data from high-dimensional (64 dimensions) space into lower-dimensional principal component space (2 dimensions). We’ll color the data points by their corresponding digit class, and thus see how well these classes have been separated in principal component space.

    # The first two principal components are generated below (on the non-standardized data)
    pca2_result = PCA(n_components=2).fit_transform(digits.data) 
    plt.scatter(pca2_result[:, 0], pca2_result[:, 1],
                    c=digits.target, edgecolor='none', alpha=0.5,
                    cmap=plt.cm.get_cmap('tab10', 10))
    plt.xlabel('Component 1')
    plt.ylabel('Component 2')
    plt.colorbar()
    plt.show()

<img src="/assets/img/pca_digits_non_standardized.png" width="700" height="500">

In this example, the explained variation per principal component is ~12% and ~9%. Thus, the first two principal components account for ~22% of the variation in the entire dataset. See the code below:

    pca = PCA(n_components=2)  
    pca_result = pca.fit_transform(data)  
    print ('Explained variation per principal component: 	{}'.format(pca.explained_variance_ratio_))  
    print ('The first two components account for {:.0f}% of the variation in the 	entire dataset'.format((pca.explained_variance_ratio_[0] + 	pca.explained_variance_ratio_[1])*100))

And ~50% of the variance is contained within 5 principal components. One may estimate how many components are needed to describe the data by assessing the cumulative explained variance ratio as a function of the number of components. Approximately 90% of the variance is retained within 20 components, whereas 75% is retained within 10 components. See the code and corresponding plot below of the cumulative sum of the percentage of variance explained by the components.

    pca4 = PCA().fit(digits.data) # Conducting PCA without specifying a number of
    plt.plot(np.cumsum(pca4.explained_variance_ratio_)) # The cumulative sum of the percentage of variance explained by all of the components
    plt.xlabel('Number of components')
    plt.ylabel('Cumulative explained variance')
    plt.title('Number of components vs. retained variance')
    plt.show() 

<img src="/assets/img/n_comp_var.png" width="650" height="500">

This data from scikit-learn is stored in the .data member, which is an (n_samples, n_features) array. This array changed from the initial shape of the array (1797, 64) to the current shape (1797, 2) via PCA reduction. Thus, the vector length has now been compressed to a length of 2. But has important data been lost by conducting this PCA reduction? Let’s plot the inverse transform of the PCA reduced data below the original data. The inverse transform can illustrate the reconstructed images after having undergone PCA reduction.

Here is an example of the original data:
<img src="/assets/img/digits0_9_original.png" width="800" height="180">
Here is that same example seen directly above, but this figure is the inverse transform of the data:
<img src="/assets/img/digits0_9_pca.png" width="800" height="180">
The original 64 dimensions were reduced to 2. While PCA has reduced the dimensionality of the data by a factor of 32, the reconstructed images contain enough information that one might visually recognize the individual digits in the figure. Although it doesn’t seem that much of the variance is retained (~22%) within the first two principal components, it does appear that PCA reduction works for this dataset.