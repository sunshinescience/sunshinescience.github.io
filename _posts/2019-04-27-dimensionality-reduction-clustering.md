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
For this demonstration, the handwritten digits dataset from scikit-learn will be used. Scikit-learn has a copy of the test set of the UCI ML hand-written digits [dataset](http://archive.ics.uci.edu/ml/datasets/Optical+Recognition+of+Handwritten+Digits). An example to load and visualize the data via scikit-learn can be found [here](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_digits.html). The dataset characteristics on scikit-learn can be found in section [5.2.4](https://scikit-learn.org/stable/datasets/index.html#digits-dataset).

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

A figure of four of each of the nine digits is shown in order to illustrate differences contributed by participants whom had written the digits. The ticks and corresponding tick labels have been removed for ease of view. See the plot and corresponding code below.

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

    def plot_dim_red(dim_red, data, title=None, xlabel=None, ylabel=None):
        """
        Scatter plot of dimensionality reduced data.
        dim_red: dimensionality reduced data
        data: data
        title: string of the title
        xlabel: string of the x label
        ylabel: string of the y label
        """
        plt.scatter(dim_red[:, 0], dim_red[:, 1], c=digits.target, cmap=plt.cm.get_cmap('tab10', 10), marker='o', edgecolor='none', alpha=0.5) 
        plt.colorbar(ticks=range(10))
        plt.clim(-0.5, 9.5)
        if xlabel:
            plt.xlabel('Component 1')
        if ylabel:
            plt.ylabel('Component 2')
        if title:
            plt.title(title)
        plt.show()

    plot_dim_red(pca2_result, data, 'PCA reduced handwritten digits', 'Component 1', 'Component 2')

<img src="/assets/img/pca_digits_non_standardized.png" width="700" height="500">

In this example, the explained variation per principal component is ~12% and ~9%. Thus, the first two principal components account for ~22% of the variation in the entire dataset. See the code below:

    pca = PCA(n_components=2) # The original 64 dimensions are reduced to 2   
    pca_result = pca.fit_transform(data) # Now we have the reduced feature set  
    print ('Explained variation per principal component: {}'.format(pca.explained_variance_ratio_))  
    print ('The first two components account for {:.0f}% of the variation in the entire dataset'.format((pca.explained_variance_ratio_[0] + pca.explained_variance_ratio_[1])*100))

And ~50% of the variance is contained within 5 principal components. One may estimate how many components are needed to describe the data by assessing the cumulative explained variance ratio as a function of the number of components. Approximately 90% of the variance is retained within 20 components, whereas 75% is retained within 10 components. See the code and corresponding plot below of the cumulative sum of the percentage of variance explained by the components.

    pca4 = PCA().fit(digits.data) # Conducting PCA without specifying a number of
    plt.plot(np.cumsum(pca4.explained_variance_ratio_)) # The cumulative sum of the percentage of variance explained by all of the components
    plt.xlabel('Number of components')
    plt.ylabel('Cumulative explained variance')
    plt.title('Number of components vs. retained variance')
    plt.show() 

<img src="/assets/img/n_comp_var.png" width="650" height="500">

This data from scikit-learn is stored in the .data member, which is an (n_samples, n_features) array. This array changed from the initial shape of the array (1797, 64) to the current shape (1797, 2) via PCA reduction. Thus, the vector length has now been compressed to a length of 2. But has important data been lost by conducting this PCA reduction? Let’s plot the inverse transform of the PCA reduced data below the original data. The inverse transform can help illustrate the reconstructed image data after having undergone PCA reduction.

    percnt = 2 
    pca3 = PCA(n_components=percnt).fit(digits.data) 
    pca3_result = pca3.transform(digits.data)
    # Transform the PCA reduced data back to its original space. 
    pca_inversed = pca3.inverse_transform(pca3_result)  # Use the inverse of the transform to reconstruct the reduced digits

    # Figure of original images, as assigned to the variable called inversed_lst
    inversed_lst = range(0, 10)
    fig = plt.figure(figsize=(10,2))
    plt_index = 0
    for i in inversed_lst:
        plt_index = plt_index + 1
        ax = fig.add_subplot(1, 10, plt_index)
        ax.imshow(digits.images[i], cmap=plt.cm.gray_r, interpolation='nearest')
    plt.tight_layout()
    plt.show() 

    # Figure of images that have undergone PCA reduction, as assigned to the variable called inversed_lst. The inverse transform is plotted here
    fig = plt.figure(figsize=(10,2))
    plt_index = 0
    for i in inversed_lst:
        plt_index = plt_index + 1
        ax = fig.add_subplot(1, 10, plt_index)
        ax.imshow(pca_inversed[i].reshape(8, 8), cmap=plt.cm.gray_r, interpolation='nearest')
    plt.tight_layout()
    plt.show() 

Here is an example of the original data:
<img src="/assets/img/digits0_9_original.png" width="800" height="180">
Here is that same example seen directly above, but this figure is the inverse transform of the data:
<img src="/assets/img/digits0_9_pca.png" width="800" height="180">
The original 64 dimensions were reduced to 2. While PCA has reduced the dimensionality of the data by a factor of 32, the reconstructed images contain enough information that one might visually recognize the individual digits in the figure. Although it doesn’t seem that much of the variance is retained (~22%) within the first two principal components, it does appear that PCA reduction works for this dataset.

### T-distributed Stochastic Neighbor Embedding
T-distributed Stochastic Neighbor Embedding (t-SNE) is a popular dimensionality reduction that helps identify patterns in data. This technique visualizes high-dimensional data via giving each individual data point a location within a two or three-dimensional map ([van der Maaten & Hinton 2008](http://jmlr.csail.mit.edu/papers/volume9/vandermaaten08a/vandermaaten08a.pdf)). 

One of the main advantages of using this algorithm is its ability to preserve local structure.  This basically means that points which are close to one another in the high-dimensional dataset will likely be close to one another in the map. 

The t-SNE algorithm undergoes two main stages: 
-  First, the algorithm models the probability distribution of neighbors around each point (‘neighbors’ refers to a set of points that are closest to each point). Similar points have a high probability of being picked whereas dissimilar points have a small probability of being picked. 
-  Second, the algorithm defines a similar probability distribution over the points in the low-dimensional map. And it minimizes the [Kullback–Leibler divergence](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) between the two distributions with respect to the locations of the points in the map. The Kullback-Leibler divergence (KL divergence) is a measure of how one probability distribution diverges from a second, expected probability distribution. So, it tries to minimize the difference between the similarities in higher-dimensional and lower-dimensional space for a representation of data points in lower-dimensional space.

With just using t-SNE on the dataset, it appears that the images corresponding to the different digits have been separated into different clusters of points. A clustering algorithm might select the separate clusters from this and one could possible assign new points to a label. While t-SNE plots often seem to display clusters, the visual clusters can be influenced by the selected parameterization and thus an understanding of the parameters for t-SNE is needed. These ‘clusters’ can be shown to appear in non-clustered [data]( https://stats.stackexchange.com/questions/263539/clustering-on-the-output-of-t-sne/264647#264647) and therefore may not be accurate. Investigation may be needed to choose parameters and [validate](https://distill.pub/2016/misread-tsne/) the results. To illustrate this, the n_iter parameter (maximum number of iterations for the optimization) for the algorithm is set at 1000 here: 

    # t-SNE reduced data
    n_iter = 1000
    n_perplexity = 40

    t_sne = TSNE(n_components=2, perplexity=n_perplexity, n_iter=n_iter)
    tsne_result = t_sne.fit_transform(data) # This is t-SNE reduced data

    # Plot of t-SNE reduced data 
    plot_dim_red(tsne_result, data, 't-SNE reduced handwritten digits')

<img src="/assets/img/t_sne_result.png" width="650" height="500">

Whereas, the n_iter parameter is set at 10000 here:

    # t-SNE reduced data
    n_iter = 10000
    n_perplexity = 40

    t_sne = TSNE(n_components=2, perplexity=n_perplexity, n_iter=n_iter)
    tsne_result = t_sne.fit_transform(data) # This is t-SNE reduced data

    # Plot of t-SNE reduced data 
    plot_dim_red(tsne_result, data, 't-SNE reduced handwritten digits')

<img src="/assets/img/t_sne_result10000.png" width="650" height="500">

And the figure below shows perplexity values in ranges varying from (2 to 100). Some of the plots show the clusters, although with very different shapes. The plot with the perplexity equal to 2 shows merged clusters, so it may be better to have a higher perplexity.

    # Figure of multiple plots of different perplexities
    perplex_lst = (2,5,30,50,100)
    fig = plt.figure(figsize=(10,2.2))
    plt_index = 0
    for i in perplex_lst:
        plt_index = plt_index + 1
        t_sne = TSNE(n_components=2, perplexity=i, n_iter=n_iter)
        tsne_result = t_sne.fit_transform(data) # This is t-SNE reduced data
        ax = fig.add_subplot(1, 5, plt_index)
        ax.scatter(tsne_result[:, 0], tsne_result[:, 1],
                    c=digits.target, edgecolor='none', alpha=0.5,
                    cmap=plt.cm.get_cmap('tab10', 10), marker='.') 
        ax.set_title('perplexity = {}'.format(i))
        ax.set_yticklabels([]) # Turn off y tick labels
        ax.set_xticklabels([]) # Turn off x tick labels
        ax.set_yticks([]) # Turn off y ticks
        ax.set_xticks([]) # Turn off x ticks
    plt.tight_layout()
    plt.show()

<img src="/assets/img/t_sne_multi_perplexity.png">

For this demonstration, t-SNE appears to have found structure in this dataset. From now on, unless otherwise stated, results from 1,000 iterations and a perplexity of 40 will be shown when using t-SNE dimensionality reduction.

### Truncated singular value decomposition (truncated SVD)
The [truncated singular value decomposition](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) estimator performs linear dimensionality reduction. Singular value decomposition ([SVD](https://en.wikipedia.org/wiki/Singular_value_decomposition#Truncated_SVD)) produces a factorization of a matrix, in which the number of columns is equal to the specified truncation. And matrix decomposition (or matrix factorization) involves describing a given matrix using its constituent elements. For ([example](https://www.oreilly.com/library/view/scikit-learn-cookbook/9781783989485/ch01s13.html)), given an n x n matrix, SVD would produce matrices with n columns. However, [Truncated SVD](https://link.springer.com/article/10.1007/BF01937276) would produce matrices with the specified number of columns, thereby reducing the dimensionality. One of the advantages in using this method is that it can operate on [sparse matrices](https://en.wikipedia.org/wiki/Sparse_matrix). Let's fit the model and examine the results:

    # Truncated SVD reduced data
    svd = TruncatedSVD(n_components=2, n_iter=10)
    svd_result = svd.fit_transform(data)  

    # Plot of Truncated SVD reduced data 
    plot_dim_red(svd_result, data, 'Truncated SVD reduced handwritten digits')

<img src="/assets/img/Truncated_SVD_result.png" width="650" height="500">

It appears that results from this transformer are similar to results from using PCA above. 

### Isometric Mapping
Isometric Mapping ([isomap](https://web.mit.edu/cocosci/Papers/sci_reprint.pdf)) is a non-linear dimensionality reduction method that uses local metric information to learn the underlying geometry of a dataset. It provides a method for estimating the geometry of a data manifold based on a rough estimate of each data point’s neighbors on the manifold. This method combines some of the features of PCA and Multidimensional scaling ([MDS](https://en.wikipedia.org/wiki/Multidimensional_scaling)).

This algorithm has three [stages](https://web.mit.edu/cocosci/Papers/nips02-localglobal-in-press.pdf). It starts by determining a neighborhood graph for the data points. Next, it computes the geodesic [distance](https://en.wikipedia.org/wiki/Distance_(graph_theory)) for all pairs of data points. Finally, through applying MDS to the shortest-path distance matrix, it constructs the low dimensional embedding of the data in Euclidean space. This is what the algorithm produces with this dataset:

    # Isomap reduced data
    iso = Isomap(n_components=2)
    iso_result = iso.fit_transform(data)

    # Plot of isomap reduced data 
    plot_dim_red(iso_result, data, 'Isomap reduced handwritten digits')

<img src="/assets/img/isomap_result.png" width="650" height="500">

(A PY file with more code (Python) is available on this [github repo](https://github.com/sunshinescience/dim_red_cluster/blob/master/dim_red_cl.py))

