---
layout: post
title: "Deep learning image classification using fast.ai - in progress"
---

Python is the most commonly used language for deep learning. Many deep learning libraries have Python options available. There are several libraries available, such as TensorFlow (Google), PyTorch (Facebook), MxNet (University of Washington, adapted by Amazon), CNTK (Microsoft), DeepLearning4j (Skymind), Caffe2 (Facebook), Nnabla (Sony), PaddlePaddle (Baidu), and Keras (a high-level API that runs on top of several libraries in this list). 

I just started Jeremy Howard’s deep learning [course](https://course.fast.ai/videos/?lesson=1). So, let's start going through the deep learning workflow for image classification using fast.ai to classify pets, which is directly from the fast.ai course (lesson 1) found [here](https://course.fast.ai/videos/?lesson=1). 

For further reading, the original paper that determines the breed of animal from an image can be found [here](http://www.robots.ox.ac.uk/~vgg/publications/2012/parkhi12a/parkhi12a.pdf).

### What is fast.ai?
The fastai library is helping make deep learning easier to use. fast.ai can be described as a research lab that includes courses, a user-friendly Python library which simplifies training fast and accurate neural nets.

Most importantly, fast.ai follows the "top down" approach. So, we start by trying to work it, without worrying about the details and the meaning behind everything. Once we are confident we can use it, we learn the details to gain a better understanding. Moreover, fast.ai allows us to build a model using only a few lines of code. After that, we can improve the model further. 

### Getting started
We've chosen to start with Google Colab as the GPU is free. We aren't sure how it will work, as it is mainly built for Tensorflow (Google's deep learning toolkit), whereas the fast.ai library sits on top of PyTorch. We'll see if we can make it work for fast.ai and if not, then we'll try something else. 

In order to get set up and access the Colab notebook from the course, follow the steps found [here](https://course.fast.ai/start_colab.html). This will iunclude information on accessing Colab, configuring your notebook instance, and how to save your work. 

If your computer doesn't have a GPU, there are several options to try if you wanted to try a different GPU, see [here](https://course.fast.ai/) for a jupyter notebook. You may also want to try a GPU from [Crestle](https://crestle.ai/), or set up an Azure Virtual Machine [(AVM)](https://azure.microsoft.com/en-gb/free/virtual-machines/search/?&ef_id=Cj0KCQiAxrbwBRCoARIsABEc9si-9bgWZ9YukU-Me3B96lDIb6SzTOHUybG_ZBnA7q0wnjqXjV8gwuwaAjJlEALw_wcB:G:s&OCID=AID2000125_SEM_4QqQWq38&MarinID=4QqQWq38_324561106063_azure%20virtual%20machine_e_c__66077404040_aud-411816020291:kwd-296465860779&lnkd=Google_Azure_Brand&dclid=CMe-yvvd5OYCFdYw0wodGbsAUA) cloud instance, or try [Paperspace](https://www.paperspace.com/), or you can even build your own deep learning machine (for example blog posts, see [here]https://www.topbots.com/deep-confusion-misadventures-in-building-a-machine-learning-server/, [here](https://medium.com/impactai/setting-up-a-deep-learning-machine-in-a-lazy-yet-quick-way-be2642318850), or [here](https://towardsdatascience.com/building-your-own-deep-learning-box-47b918aea1eb)).

For our Colab notebook, at the beginning of the notebook, two code cells have been included, the first is:

    !curl -s https://course.fast.ai/setup/colab | bash

The second cell is:

    from google.colab import drive
    drive.mount('/content/gdrive', force_remount=True)
    root_dir = "/content/gdrive/My Drive/"
    base_dir = root_dir + 'fastai-v3/'

Go to the URL in a browser, then choose your account to continue to Google Drive File Stream. Click `Allow` so that Google Drive File Stream can access your Google Account. Copy the code and then go back to your Colab notebook and paste your authorization code.

### Lesson 1 notebook
After the two code blocks have been run (from the section above), each notebook starts with the lines of code below:

    %reload_ext autoreload
    %autoreload 2
    %matplotlib inline

This code ensures that any updates to the library should be automatically reloaded and refreshed. And all plots from matplotlib will be displayed inside the notebook.

We'll be working with the [fastai library v1](https://www.fast.ai/2018/10/02/fastai-ai/) and using the [Pytorch v1](https://hackernoon.com/pytorch-1-0-468332ba5163) deep learning framework. Let's import the necessary packages:

    from fastai.vision import *
    from fastai.metrics import error_rate

As of today, you'll get a VersionConflict error message. For issues related to versions, see [here](https://forums.fast.ai/t/fastai-v0-7-install-issues-thread/24652) to install an earlier version of fast.ai if needed. Also, try having a look at [this](https://stackoverflow.com/questions/59513091/versionconflict-in-fastprogress/59549564#59549564)

If you're using a computer with an unusually small GPU, you may get an out of memory error. If this happens, use a smaller batch size and run it again. First, let's use a batch size of 64:

    bs = 64

#### Obtaining the data
The fast.ai datasets can be found [here](https://course.fast.ai/datasets). The dataset we'll be using is the [Oxford-IIIT Pet Dataset ](http://www.robots.ox.ac.uk/~vgg/data/pets/), which has 37 distinct categories of pets, 25 dogs and 12 cat breeds.

In order to download and extract the data, we'll use the `untar_data` function, and we need to pass in a URL.

    path = untar_data(URLs.PETS)
    path


<center>Happy coding!<center>

<center>.           .           .<center>


code:
    !curl -s https://course.fast.ai/setup/colab | bash

    from google.colab import drive
    drive.mount('/content/gdrive', force_remount=True)
    root_dir = "/content/gdrive/My Drive/"
    base_dir = root_dir + 'fastai-v3/'

markdown:
    Don’t forget to append base_dir before root path(s) in all notebooks.

    For example, in lesson2-download.ipynb 5th cell, make the following changes:

        path = Path(base_dir + 'data/bears')
        dest = path/folder
        dest.mkdir(parents=True, exist_ok=True)

markdown:
    Welcome to lesson 1! For those of you who are using a Jupyter Notebook for the first time, you can learn about this useful tool in a tutorial we prepared specially for you; click File->Open now and click 00_notebook_tutorial.ipynb.

    In this lesson we will build our first image classifier from scratch, and see if we can achieve world-class results. Let's dive in!

    Every notebook starts with the following three lines; they ensure that any edits to libraries you make are reloaded here automatically, and also that any charts or images displayed are shown in this notebook.

code:
    %reload_ext autoreload
    %autoreload 2
    %matplotlib inline

    # Check the version of fastai that you import
    import fastai
    fastai.__version__