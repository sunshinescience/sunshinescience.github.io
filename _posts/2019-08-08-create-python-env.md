---
layout: post
title: "Creating a Python Virtual Environment for a project"
---

The virtual environment (virtualenv) is a copy of an existing version of Python that has the option to inherit existing packages. A virtualenv has its its own installation directories, which doesn’t share libraries with other environments (and optionally doesn’t access the globally installed libraries). 

Virtualenvs allow you to not affect pre-installed Python system packages, which otherwise could break some of your system tools and scripts. So, it could be beneficial to have one or more Python environments where you can experiment with different combinations of packages, *without* affecting your main installation. 

Using a virtualenv would allow you to install packages normally with Anaconda. Depending on your operating system, you may need to create this in different ways. If Anaconda is not already installed, download it [here](https://docs.conda.io/projects/conda/en/latest/user-guide/install/).

### Requirements
  [Anaconda Distribution](https://docs.anaconda.com/anaconda/) should be installed and accessible.

### Check that conda is installed and in your path
Open up a terminal. Type the command `conda -V` in the command line and press enter. If conda is installed, you should see the version. To update conda, type `conda update conda` and update any necessary packages by typing `y` to finish.

### Create the virtual environment for your project
To create a virtualenv, see the documentation [here](https://docs.python.org/3/library/venv.html#module-venv).

If you have a project in a directory, access that directory (e.g., if you have a directory called `my_project`, from the command line type cd my_project). Then, create a .yml file (e.g., create a file called environment.yml) in that directoy by typing the following in the command line:

    touch environment.yml

Type in the file called environment.yml any needed channels/dependencies, for example, see [here](https://github.com/sunshinescience/dim_red_cluster/blob/master/environment.yml).

Next, in the terminal run the following code:

    conda env create -f 

### Activate an environment
To activate or switch into this environment, type the following where `environment` here is the name you gave to your environment when you created it:

    conda activate environment

In order to install additional Python packages in the environment, enter the following command, where environment is the name of your environment and [package] is the name of the package you want to install:

    conda install -n environment [package]

### Update the environment
To update the environment, use:

    conda env update -f environment.yml

### Deactivate an environment
To deactivate an active environment, use:

    conda deactivate

### Delete an environment that is no longer needed
If you wish to delete an a virtual environment (in this example, the environment is called 'environment') that is no longer needed, type in the command line:

    conda remove -n environment -all

### Detailed installation and user guide
The virtual environment installation documentation can be found [here](https://virtualenv.pypa.io/en/stable/installation/). And a user guide can be found in the documentation [here](https://virtualenv.pypa.io/en/stable/userguide/). 