---
layout: post
title: "Dimensionality Reduction and Clustering on a three-dimensional Dataset"
---
<p align="center"><img src="/assets/img/example_block.png"></p>

In this project, we'll aim to investigate unlabelled three-dimensional data via dimensionality reduction and subsequent clustering methods. The goal of clustering is to identify structure in data. And dimensionality reduction might improve the clustering of the data. 

We'll analyze synthetic data assembled for the fictitious field referred to as “Olympus.” The OLYMPUS reservoir model was developed for an optimization benchmark [challenge](http://www.isapp2.com/optimization-challenge/motivation-and-background.html).

To analyze trends in the data, we will use similar techniques of the demonstration found [here](https://sunshinescience.github.io/2019/04/27/dimensionality-reduction-clustering.html). The [scikit-learn](https://scikit-learn.org/stable/) library is a core machine learning tool for Python. We'll use the following algorithms from scikit-learn:
  
  [Dimensionality reduction](https://scikit-learn.org/stable/modules/unsupervised_reduction.html)
-    Principal component analysis, t-distributed Stochastic Neighbor Embedding, truncated singular value decomposition, and Isomap 
  
  [Clustering](https://scikit-learn.org/stable/modules/clustering.html#clustering)
-    K-means, spectral, and affinity propagation clustering

### Summary of Results
After assessing the data, the conclusion reached is that isomap dimensionality reduction combined with either k-means or spectral clustering appears to have found a natural grouping in the data. 

For further details, please refer to the full analysis below. The detailed code can be found [here](https://github.com/sunshinescience/dim_red_cl_blocks/blob/master/dim_red_cl_3dBlocks.py).

## Exploring the dataset
The [files](http://www.isapp2.com/) that were prepared for the ISAPP field development optimization challenge were provided on a strict “as is” basis.  The dataset is available for non-commercial purposes only and to request the data set, refer to the contact page of their [website](http://www.isapp2.com/contact.html). Subsets of the models have been converted into structured regular tensors. [Tensors](https://en.wikipedia.org/wiki/Tensor) are matrices that are represented using n-dimensional arrays. 

Let's import several useful libraries:

    import sys

    import numpy as np
    import matplotlib.pyplot as plt
    from mpl_toolkits.mplot3d import Axes3D

    from sklearn.preprocessing import scale
    from sklearn.manifold import TSNE
    from sklearn.decomposition import TruncatedSVD
    from sklearn.manifold import Isomap
    from sklearn.decomposition import PCA

    from sklearn.cluster import KMeans
    from sklearn.cluster import MeanShift
    from sklearn.cluster import estimate_bandwidth
    from sklearn.cluster import SpectralClustering
    from sklearn.cluster import DBSCAN
    from sklearn.cluster import AffinityPropagation

Data standardization is the process of transforming features so that each feature looks like standard normally distributed data. Each feature is distributed about zero with a standard deviation of 1. The [sklearn.preprocessing](https://scikit-learn.org/stable/modules/preprocessing.html) package contains functions and classes to change features into a representation that would be useful for learning algorithms. 

Let's load and standardize the dataset:

    # Load the dataset
    data = np.load("tensors_OLYMPUS1.npy")
    tensors = data

    # standardize the data 
    data = data.reshape((data.shape[0], -1))
    data = scale(data)

    # Number of samples
    n_samples = data.data.shape[0]

Here, the data will be visualized in three-dimensions (3-D). Each 3-D data point here is shown as a 'block.' And one *block* is shown on each plot below. Some of the data are plotted as 3D volumetric objects using [voxels](https://matplotlib.org/gallery/mplot3d/voxels.html), which are shown below:

    indexes = 8 # indexes is the number of plots
    fig_size = (10,8)

    n_rand=[]
    for i in range(indexes):
        n_rand.append(np.random.randint(0, n_samples))
    print (n_rand)

    fig = plt.figure(figsize=fig_size)
    plt_index = 0
    for i in n_rand:
        tensor = tensors[i]
        voxels = tensor > 0.0
        plt_index = plt_index + 1
        ax = fig.add_subplot((indexes-(indexes/2)), 2, plt_index, projection='3d')
        ax.voxels(voxels, edgecolor='k', alpha=0.5) # if you want to change the face colors, use: facecolors='b',
    plt.tight_layout()
    plt.show()

<img src="/assets/img/example_8blocks.png">

## Dimensionality reduction 
[Dimensionality reduction](https://en.wikipedia.org/wiki/Dimensionality_reduction) is a process that reduces the number of features in a dataset. For comparison purposes, the following dimensionality reduction techniques will be used: principal component analysis, t-distributed Stochastic Neighbor Embedding, truncated singular value decomposition, and Isomap. 

    def plot_dim_red(dim_red, data, dim_red_name=None, title=None, xlabel=None, ylabel=None):
        """
        Scatter plot of dimensionality reduced data.
        dim_red: dimensionality reduced data
        data: data
        title: string of the title
        xlabel: string of the x label
        ylabel: string of the y label
        """
        plt.scatter(dim_red[:, 0], dim_red[:, 1], c='k', cmap=plt.cm.get_cmap('tab10', 10), marker='o', edgecolor='none', alpha=0.5) 
        plt.clim(-0.5, 9.5)
        if xlabel:
            plt.xlabel('Component 1')
        if ylabel:
            plt.ylabel('Component 2')
        if title:
            plt.title(title)
        # plt.show()
        plt.savefig('plot_{}.png'.format(dim_red_name), dpi=150)

### Principal component analysis
Principal component analysis ([PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html)) is a technique that reduces the dimensionality of a dataset by expressing the data in terms of a few well-chosen features that most efficiently capture the variation in the data. Let’s fit the model and visualize the results:

    pca = PCA(n_components=2)
    pca_result = pca.fit_transform(data)
    
    # Plot of PCA reduced data
    plot_dim_red(pca_result, data, 'PCA', 'PCA reduced Olympus data', 'Component 1', 'Component 2')

<img src="/assets/img/plot_PCA.png" width="700" height="500">

### T-distributed Stochastic Neighbor Embedding
T-distributed Stochastic Neighbor Embedding ([t-SNE](https://scikit-learn.org/stable/modules/generated/sklearn.manifold.TSNE.html)) is a popular dimensionality reduction technique that helps identify patterns in data. It helps visualize data by giving each individual data point a location within a two or three-dimensional map. The algorithm produces the following:

    n_iter = 1000
    n_perplexity = 40

    t_sne = TSNE(n_components=2, perplexity=n_perplexity, n_iter=n_iter)
    tsne_result = t_sne.fit_transform(data) # t-SNE reduced data

    # Plot of t-SNE reduced data 
    plot_dim_red(tsne_result, data, 't-SNE', 't-SNE reduced Olympus data')

<img src="/assets/img/plot_t_sne.png" width="700" height="500">

### Truncated singular value decomposition 
The truncated singular value decomposition ([truncated SVD](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html)) method performs linear dimensionality reduction via factorization of a matrix, in which the number of columns is equal to the specified truncation. Let’s use the algorithm and visualize the results:

    svd = TruncatedSVD(n_components=2, n_iter=10)
    svd_result = svd.fit_transform(data) 

    # Plot of Truncated SVD reduced data
    plot_dim_red(svd_result, data,'Truncated SVD', 'Truncated SVD reduced Olympus data')

<img src="/assets/img/plot_truncated_SVD.png" width="700" height="500">

### Isometric Mapping
Isometric Mapping ([isomap](https://scikit-learn.org/stable/modules/generated/sklearn.manifold.Isomap.html)) is a non-linear dimensionality reduction method that uses local metric information to identify the underlying geometry of a dataset. The algorithm produces the following results:

    iso = Isomap(n_components=2)
    iso_result = iso.fit_transform(data)

    # Plot of isomap reduced data 
    plot_dim_red(iso_result, data, 'Isomap', 'Isomap reduced Olympus data')

<img src="/assets/img/plot_Isomap.png" width="700" height="500">

It appears that with using isomap, some structure may have been found in this dataset. A clustering algorithm might select the separate clusters from the isomap dimensionality reduced data.

## Clustering
In order to better understand data, sometimes it helps to organize it into groups. [Clustering](https://en.wikipedia.org/wiki/Cluster_analysis) is a very popular unsupervised learning technique that is used to find logical groupings within data. For comparison purposes, the following clustering techniques will be used: k-means, spectral, and affinity propagation clustering. 

### K-means clustering
The [goal](https://pdfs.semanticscholar.org/0ec1/32fce9971d1e0e670e650b58176dc7bf36da.pdf) of [k-means](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html) clustering is to minimize the sum of squared distances between all points and the cluster centre. Note that K-means is limited to linear cluster boundaries - if the clusters have complicated geometries, K-means may not be effective.

    n_clusters = 15
    n_init = 10

    def k_means_reduced(reduced_data, initialization, n_clusters, n_init):
        """
        This returns K-means clustering on data that has undergone dimensionality reduction.
        Parameters:
            reduced_data: The data that has undergone dimensionality reduction
            initialization: Method for initialization, defaults to ‘k-means++’:
            n_clusters: The number of clusters to form as well as the number of centroids to generate.
            n_init: Number of times the k-means algorithm will run with different centroid seeds.
        """
        k_means = KMeans(init=initialization, n_clusters=n_clusters, n_init=n_init) 
        k_means_model = k_means.fit(reduced_data)
        return k_means_model

    # K-means clustering on t-SNE reduced data
    k_t_sne = k_means_reduced(tsne_result, 'k-means++', n_clusters, n_init)
    # K-means clustering on Truncated SVD reduced data
    k_SVD = k_means_reduced(svd_result, 'k-means++', n_clusters, n_init)
    # K-means clustering on isomap reduced data
    k_iso = k_means_reduced(iso_result, 'k-means++', n_clusters, n_init)
    # K-means clustering on PCA reduced data
    k_pca = k_means_reduced(pca_result, 'k-means++', n_clusters, n_init)

Here is a plot of eight random blocks from a select cluster:

    cluster_data_rand = []
    for i in range(indexes):
        rand_num = np.random.choice(cluster_data)
        cluster_data_rand.append(rand_num)

    fig = plt.figure(figsize=fig_size)
    plt_index = 0
    for i in cluster_data_rand:
        tensor = tensors[i]
        voxels = tensor > 0.0
        plt_index = plt_index + 1
        ax = fig.add_subplot((indexes-(indexes/2)), 2, plt_index, projection='3d')
        ax.voxels(voxels, edgecolor='k', alpha=0.5)
    fig_title = 'Random blocks from cluster ' + str(clust_num)
    fig.suptitle(fig_title, fontsize=12)
    plt.tight_layout()
    plt.show()

<img src="/assets/img/random_8blocks.png">

The [elbow curve](https://en.wikipedia.org/wiki/Elbow_method_(clustering)) is a method to approximate the appropriate number of clusters within a dataset. Here is a plot of an elbow curve:

    def elbow_curve(dim_red_data):
        """
        Plots an elbow curve to select the optimal number of clusters (k) for k-means clustering.
        Fit KMeans and calculate sum of squared errors (SSE) for each cluster (k), which is 
        defined as the sum of the squared distance between each member of the cluster and its centroid.
        Parameter:
            dim_red_data: Dimensionality reduced data
        """
        sse = {}
        for k in range(1, 40): 
            # Initialize KMeans with k clusters and fit it 
            kmeans = KMeans(n_clusters=k, random_state=0).fit(dim_red_data)  
            # Assign sum of squared errors to k element of the sse dictionary
            sse[k] = kmeans.inertia_ 
        # Add the plot title, x and y axis labels
        plt.title('The Elbow Method')
        plt.xlabel('Number of clusters')
        plt.ylabel('Sum of squared distances')
        # Plot SSE values for each k stored as keys in the dictionary
        plt.plot(list(sse.keys()), list(sse.values()))
        plt.show()

    elbow_curve(tsne_result)

<img src="/assets/img/elbow_curve.png">

While it might appear that the bend in the elbow indicates that about 8 clusters can be found in this dataset, the curve does not level out so it is challenging determining the number of clusters here. However, fifteen clusters are approximated, which is a little bit before the sum of squared distances indicates a relatively flatter curve in the above plot.

Visualize the results of clustering on dimensionality reduced data:

    def plot_dim_red_clust(dim_red, cluster_type, dim_red_name=None, cluster_type_name=None, centroids=None):
        """
        Visualize the results of clustering on dimesnionality reduced data.
        Plots the dimensionality reduced data vs. the clustered dimensionality reduced data
        Parameters:
            dim_red: dimensionality reduction method
            cluster_type: the type of clustering algorithm, for example, from the Scikit-learn library, one can use the k-means implementatation as sklearn.cluster.KMeans()
            dim_red_name: name of dimesnionality reduction to be in the title of the corresponding plot
            cluster_type_name: name of clustering method to be in the title of the corresponding plot
            centroids: plots the coordinates of cluster centers 
        """
        fig, axarr = plt.subplots(2, 1, figsize=(6,6))  
        # Plot of dimensionality reduced data 
        ax1 = axarr[0]
        ax1.scatter(dim_red[:,0], dim_red[:,1], c='k', marker='.')
        if dim_red_name:
            ax1.set_title('{} reduced data'.format(dim_red_name))
        # Plot of clustering on dimensionality reduced data
        ax2 = axarr[1]
        ax2.scatter(dim_red[:,0], dim_red[:,1], c=cluster_type.labels_, marker='.')
        if centroids:
            centroids = cluster_type.cluster_centers_
            ax2.scatter(centroids[:, 0], centroids[:, 1],
                        marker='x', s=100, linewidths=5,
                        c='k', zorder=10)
        if cluster_type_name:
            ax2.set_title('{} clustering on {} reduced data'.format(cluster_type_name, dim_red_name))
        plt.tight_layout()
        plt.show()

K-means clustering on the various dimensionality reduction methods are visualized:

    # Plot of k-means clustering on t-SNE reduced data
    plot_dim_red_clust(tsne_result, k_t_sne, 't-SNE', 'K-means', centroids=True)

<img src="/assets/img/plot_t-SNE_K-means.png" width="900" height="350">

    # Plot of k-means clustering on pca reduced data
    plot_dim_red_clust(pca_result, k_pca, 'PCA', 'K-means')

<img src="/assets/img/plot_PCA_K-means.png" width="900" height="350">

    # Plot of K-means clustering on truncated SVD reduced data
    plot_dim_red_clust(svd_result, k_SVD, 'truncated SVD', 'K-means')

<img src="/assets/img/plot_truncated SVD_K-means.png" width="900" height="350">

    # Plot of K-means clustering on isomap reduced data
    plot_dim_red_clust(iso_result, k_iso, 'isomap', 'K-means')

<img src="/assets/img/plot_isomap_K-means.png" width="900" height="350">

### Spectral clustering
The [spectral](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.SpectralClustering.html) clustering approach can be used to identify communities of nodes in a graph, based on the edges connecting them. 

    sc_tsne = SpectralClustering(n_clusters=n_clusters, affinity='nearest_neighbors',
                            assign_labels='discretize').fit(tsne_result)  
    sc_iso = SpectralClustering(n_clusters=n_clusters, affinity='nearest_neighbors',
                            assign_labels='discretize').fit(iso_result)  
    sc_pca = SpectralClustering(n_clusters=n_clusters, affinity='nearest_neighbors',
                            assign_labels='discretize').fit(pca_result)  
    sc_svd = SpectralClustering(n_clusters=n_clusters, affinity='nearest_neighbors',
                            assign_labels='discretize').fit(svd_result)  

    # Plot the results of spectral clustering on t-SNE reduced data
    plot_dim_red_clust(tsne_result, sc_tsne, 't-SNE', 'Spectral') 

<img src="/assets/img/plot_t-SNE_Spectral.png" width="900" height="350">

    # Plot the results of spectral clustering on isomap reduced data
    plot_dim_red_clust(iso_result, sc_iso, 'isomap', 'Spectral')

<img src="/assets/img/plot_isomap_Spectral.png" width="900" height="350">

    # Plot the results of spectral clustering on PCA reduced data
    plot_dim_red_clust(pca_result, sc_pca, 'pca', 'Spectral')

<img src="/assets/img/plot_pca_Spectral.png" width="900" height="350">

    # Plot the results of spectral clustering on truncated SVD reduced data
    plot_dim_red_clust(svd_result, sc_svd, 'truncated SVD', 'Spectral')

<img src="/assets/img/plot_truncated SVD_Spectral.png" width="900" height="350">

### Affinity propagation clustering
The [affinity propogation](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.AffinityPropagation.html) clustering [approach](https://warwick.ac.uk/fac/sci/dcs/research/combi/seminars/freydueck_affinitypropagation_science2007.pdf) works by grouping the data by passing messages between the data points.

    ap_tsne = AffinityPropagation().fit(tsne_result)
    ap_iso = AffinityPropagation().fit(iso_result)
    ap_pca = AffinityPropagation().fit(pca_result)
    ap_svd = AffinityPropagation().fit(svd_result)

    # Plot the results of Affinity propogation clustering on t-SNE reduced data
    plot_dim_red_clust(tsne_result, ap_tsne, 't-SNE', 'Affinity propogation', centroids=True) 

<img src="/assets/img/plot_t-SNE_Affinity propogation.png" width="900" height="350">

    # Plot the results of Affinity propogation clustering on isomap reduced data
    plot_dim_red_clust(iso_result, ap_iso, 'isomap', 'Affinity propogation', centroids=True) 

<img src="/assets/img/plot_isomap_Affinity propogation.png" width="900" height="350">

    # Plot the results of Affinity propogation clustering on PCA reduced data
    plot_dim_red_clust(pca_result, ap_pca, 'PCA', 'Affinity propogation', centroids=True) 

<img src="/assets/img/plot_PCA_Affinity propogation.png" width="900" height="350">

    # Plot the results of Affinity propogation clustering on truncated SVD reduced data
    plot_dim_red_clust(svd_result, ap_svd, 'truncated SVD', 'Affinity propogation', centroids=True) 

<img src="/assets/img/plot_truncated SVD_Affinity propogation.png" width="900" height="350">

### Conclusions
It appears that results from clustering using the truncated SVD dimensionality reduced data are similar to results from using the PCA dimensionality reduced data. For affinity propogation clustering, the number of clusters vary from 29 to 33 using the different dimensionality reduction techniques here. This appears to be nearly double the amount of clusters obtained from spectral clustering using the different dimensionality techniques in this demonstration. The number of clusters in k-means clustering was estimated as 15 prior to using that algorithm, based on the elbow curve. K-means clustering is comparable to that obtained via spectral clustering for the Olympus dataset. It is possible that the data may need to be processed further to enable better clustering. But, for this unlabelled three-dimensional data, isomap dimensionality reduction coupled with k-means or spectral clustering does appear to find a natural grouping in the data. Perhaps a future goal could be to try to label some of this data in order to try to determine how accurately this data has been clustered.