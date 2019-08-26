---
layout: post
title: "Creating a data set via Web Scraping with Python - in progress"
---

<img src="/assets/img/freestock_56437381.jpg">
Photo from [https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381](https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381)

[Web scraping](https://en.wikipedia.org/wiki/Web_scraping) is a technique that is used to extract data from websites. Sometimes it can be beneficial to gather and copy data from the web. But, if there isn't a direct link to download it, web scraping can be performed to obtain the data. Mostly everything that can be found online is scrape-able. Scraping works by sending a request to a server and it receives a response made up of the HTML, CSS, and etc. that make up that page. Then it finds the information that you want in that response code.

For this demonstration, data from one crowd funding website (called Indiegogo) will be gathered, organized, and cleaned. The main goal of this demonstration is to obtain the unstructured data via web scraping and build a structured data set. Some additional sites might be added later on, which include those mentioned in [this](https://www.investopedia.com/articles/personal-finance/091415/8-best-alternatives-kickstarter.asp) article. 

It appears that there are (at least) two ways to accomplish this goal using Python. [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) is a user-friendly Python library that can be used to parse HTML and XML files. [Scrapy](https://docs.scrapy.org/en/latest/) is a high-level web crawling and web scraping framework. Here, scrapy has been chosen to scrape the website. 

### Installing scrapy
It is recommended that one should install scrapy within a “virtual environment” ([virtualenv](https://virtualenv.pypa.io/en/latest/)). A virtualenv is an environment with its own installation directories, which doesn’t share libraries with other virtualenv environments (and optionally doesn’t access the globally installed libraries). The virtual environment installation documentation can be found [here](https://virtualenv.pypa.io/en/stable/installation/) and a user guide can be found [here](https://virtualenv.pypa.io/en/stable/userguide/). An example of how to set up a virtual environment on a mac can be found in [this](https://sunshinescience.github.io/2019/08/08/create-python-env.html) blog post.

A scrapy tutorial can be found in the documentation [here](https://doc.scrapy.org/en/latest/intro/tutorial.html). To install Scrapy via the conda-forge channel, type in the terminal (mac/linux) or command line (windows):

    conda install -c conda-forge scrapy

Type `y` to proceed with necessary packages. 

### Create a new project
Navigate into a directory where you'd like to store your code (here the new project will be called 'demo_scrapy' and type:

    scrapy startproject demo_scrapy

This created a demo_scrapy directory with the following contents:

    demo_scrapy/              # Project root directory
        scrapy.cfg            # Contains the configuration information to deploy the spider

        demo_scrapy/          # Project's Python module (you'll import your code from here)
            __init__.py

            items.py          #  Describes the definition of each item that we’re scraping

            middlewares.py    # Project middlewares file

            pipelines.py      # Project pipelines file

            settings.py       # Project settings file

            spiders/          # All the spider code goes into this directory (we will be using this)
                __init__.py


### Get a list of start url's
start_urls is a list of the [URLs](https://en.wikipedia.org/wiki/URL) which the spider will *start* to crawl from. In order to get individual campaign links, we will use each element in this list of the URLs. 

On the website [www.indiegogo.com](https://www.indiegogo.com/), the explore tab is selected, followed by explore all projects. You can sort by *trending* or *most funded*. There are three main categories to choose from, each having several subcategories (e.g., Tech & Innovation has Audio as the first subcategory). Depending on which category is chosen, you can see the URL here:

<p align="center"><img src="/assets/img/indiegogo_category_1.png" width="600" height="550"></p>

Note that the above image also highlights in blue the selected category (*Audio*) as well as what it is sorted by (*Trending*). The first URL in our list of start_urls is this:

https://www.indiegogo.com/explore/audio?project_type=campaign&project_timing=all&sort=trending

### Find individual campaign links
You can use your browser's [development tools](https://docs.scrapy.org/en/latest/topics/developer-tools.html) for web scraping. Here, we'll use Google Chrome, so right click on the individual campaign and click 'inspect' to see where the campaigns are located within the code (see image below).In firefox, right click on the web page and click 'inspect element.' Firefox then shows you the code that it is executing to generate that page. 

<p align="center"><img src="/assets/img/indiegogo_indiv_campaign_lrge.png"></p>

#### XPath
In this demonstration, [XPath](https://en.wikipedia.org/wiki/XPath) is used to direct our scraper to a specific part of the HTML. For example, we would use XPath to extract a partial URL from the code shown in the image below, such as:

/projects/asivio-one-best-wrieless-earbuds-with-160h-play--3/pica

<p align="center"><img src="/assets/img/indiegogo_indiv_campaign.png"></p>

XPath stands for XML Path Language and it uses 'path-like' syntax to identify and navigate nodes in an XML document. XPath expressions can be used in Python. The path expressions to select nodes or node-sets in an XML document look simmilar to path expressions used with traditional computer file systems:

<p align="center"><img src="/assets/img/indiegogo_XPath_folder_example.png" width="350" height="75"></p>

For example, given source XML that contains:

<img src="/assets/img/indiegogo_XPath_leters_ex.png" width="200" height="200">
  
The simplest form XPath would take would be:
/A/B/C

There are several great overviews that go into a lot of detail, such as those found at [scrapy.org](https://docs.scrapy.org/en/xpath-tutorial/topics/xpath-tutorial.html), [tutorialspoint](https://www.tutorialspoint.com/xpath/xpath_expression.htm), [Wikipedia](https://en.wikipedia.org/wiki/XPath), and [w3schools](https://www.w3schools.com/xml/xpath_intro.asp).

### Create a spider
Scrapy uses spiders, which are classes that you define, to scrape information from a website (or several websites). They must subclass `scrapy.Spider`.









