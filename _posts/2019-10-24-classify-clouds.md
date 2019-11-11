---
layout: post
title: "Neural Network Cloud Classification in the Cloud - in progress"
---

<p align="center"><img src="/assets/img/blue-clouds-day-fluffy-53594.jpeg" width="500" height="400"></p>
Photo from [www.pexels.com](https://www.pexels.com/photo/nature-sky-clouds-blue-53594/)

### Understanding Clouds from Satellite Images
Given the inherent nature of climate change, this subject has impacted political decision making for several years. A current Kaggle competition involves trying to understand an important climatic variable. This includes trying to help improve scientists physical understanding of clouds, in order to build better climatic models. 

A team of 68 scientists had identified areas of cloud patterns in each image, and each image was labeled by approximately 3 different scientists. Ground truth was determined by the union of the areas marked by all labelers for that image, after removing any black band region from the areas. The cloud formations were given the following label names: Fish, Flower, Gravel, Sugar. For each image in the test set, we need to segment the regions of each cloud formation label. And each image has at least one cloud formation, and can contain up to all four.

The goal for this competition is to build a model to classify cloud organization patterns from satellite images. For this project, we'll create a neural network that can recognize cloud structures from these images. The image data from Kaggle was taken from the [NASA Worldview](https://worldview.earthdata.nasa.gov/?l=Reference_Labels(hidden),Reference_Features(hidden),Coastlines,VIIRS_SNPP_CorrectedReflectance_TrueColor(hidden),MODIS_Aqua_CorrectedReflectance_TrueColor(hidden),MODIS_Terra_CorrectedReflectance_TrueColor), for [this](https://www.kaggle.com/c/understanding_cloud_organization) kaggle competition. Here, we'll use TensorFlow in an interactive Python notebook, called Colab Notebook, which runs in Google Cloud.

### Summary of Results
...coming soon...

### Access Colab Notebook 
Here, we will use Colaboratory, which is a Jupyter notebook environment that requires no setup to use and runs entirely in the Google cloud. And it provides free GPU. Let's first set up a new Colab Notebook, which can be found [here](https://colab.research.google.com/notebooks/welcome.ipynb#recent=true). Name your new notebook. To use GPU execution in order to speed things up, select the “runtime” dropdown menu, select “change runtime type” and select GPU in the hardware accelerator drop-down menu, then click "save".

### Import dependencies
Copy and paste the following code in the first cell of your colab notebook:

    try:
    # Use the %tensorflow_version magic if in colab.
    %tensorflow_version 2.x
    except Exception:
    pass

    import tensorflow as tf
    import math
    import numpy as np
    import matplotlib.pyplot as plt

### Load data into Google Colaboratory
First get the downloaded data from Kaggle and put that data into Google Drive. Next, mount the Google Drive to Google Colab by running the following code in the Colab shell:

    #Connect gdrive into the google colab notebook
    from google.colab import drive
    drive.mount('/content/gdrive')

A link will pop up. Go to that link in a browser. Then, copy the authorization code of your account. Then, paste the code into the output colab shell and hit enter. Now your Google Drive is mounted to this location: `/content/gdrive/My Drive/`. See these steps in a post found [here](https://www.marktechpost.com/2019/06/07/how-to-connect-google-colab-with-google-drive/).

### Exploring the dataset
Machine learning uses input data called the Features and output data called the Labels in order to learn the model algorithm from. In this case, the feature is the input image and the label would be the correct output that specifies the cloud structure that the image depicts.

The satellite images in the dataset are 1400 x 2100 pixel color images. Each pixel consists of 8 bits (1 byte) for a black and white image or 24 bits (3 bytes) for a color image - one byte each for Red, Green, and Blue. As these are color images, each image is 1400 x 2100 x 3 bytes, that is 8820000 bytes. We need to create a neural network that takes the 8820000 bytes as input, and then identifies which of the four types of cloud structures (if any) are within the image. There are 22184 images in the training set (in the train.csv file), as seen using the following code:

    filename = '/content/gdrive/My Drive/Classify_clouds/train.csv'
    with open(filename) as f:
        lines_list = f.readlines()[1:]
        num_images = len(lines_list) 
        print (num_images)

Let's take a look at an example image.

    # Enter the image number (as image_index) from the train.csv file
    image_index = 0
    # Get the image name for the corresponding input image number
    image_name = lines_list[image_index + 1].strip().split(',')[0]

    # Obtain only the image number, without the designated pattern (i.e., flower, fish, etc.)
    image_name_num = image_name.split('.')[0]

    # Get the image path and print the Numpy array for the chosen image
    image_path = ''.join('/content/gdrive/My Drive/Classify_clouds/train_images/' + str(image_name_num) + '.jpg')

    img = mpimg.imread(image_path)
    print(img) # Prints a Numpy array

Each inner list represents a pixel. Here, with an RGB image, there are 3 values. Render the matplotlib image using the [imshow()](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.imshow.html#matplotlib.pyplot.imshow) function:

    # plot the example image
    imgplot = plt.imshow(img)
    plt.axis('off')
    plt.savefig('0011165.png', dpi=150)


<img src="/assets/img/0011165_Flower.png" width="700" height="500">

### Training the model
Split the data into a train dataset and a test dataset.



________
Exercises

Experiment with different models and see how the accuracy results differ. In particular change the following parameters:

    Set training epochs set to 1
    Number of neurons in the Dense layer following the Flatten one. For example, go really low (e.g. 10) in ranges up to 512 and see how accuracy changes
    Add additional Dense layers between the Flatten and the final Dense(10, activation=tf.nn.softmax), experiment with different units in these layers
    Don't normalize the pixel values, and see the effect that has

Remember to enable GPU to make everything run faster (Runtime -> Change runtime type -> Hardware accelerator -> GPU). Also, if you run into trouble, simply reset the entire environment and start from the beginning:

    Edit -> Clear all outputs
    Runtime -> Reset all runtimes
_________

<center>Happy coding!<center>

<center>.       .       .<center>