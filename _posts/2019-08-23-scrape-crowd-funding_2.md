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

### Inspect the target web page
Let's first inspect the website that we want to work with. Chrome DevTools is a set of web developer tools that is built directly into the Google Chrome browser. In Chrome's main menu, click in the upper right corner, and click on the 'More Tools' tab, and click on the 'Developer Tools' tab. This opens up the developer tools. 

 <img src="/assets/img/indiegogo_developer_tools.png">
 **Figure 1.** Open DevTools from Chrome's main menu

The tabs that we're interested in include:
-   **Elements:** allows us to inspect the DOM and see where things are and how they're positioned. 
-   **[Console](https://developers.google.com/web/tools/chrome-devtools/console/):** allows us to interact with the page, but its main uses are to view logged messages and run JavaScript. 
-   **Sources:** helps find the origin of the data. This can help you debug JavaScript code.
-   **Network:** allow us to see where the data originates from a server perspective. This categorizes and displays detailed information about each related operation on the page.
-   **Application:** 

If you click on the elements tab in DevTools, and click on the inspect tool (see red square in the image below), you can move over the page and inspect different elements. 

<img src="/assets/img/indiegogo_elements_tab.png">

And if you click on something, the HTML markup that is displaying that selection gets highlighted. 

<img src="/assets/img/indiegogo_all_categories_tab.png">

And you can see the HTML's markup path (e.g., in the red rectangle in the below image).

<img src="/assets/img/indiegogo_elemnts_path.png">

Right click on the HTML markup and select copy and copy XPath. If you paste that into the console, you can see what the XPath looks like. If you want to view the network activity that a page has, please see the tutorial found [here](https://developers.google.com/web/tools/chrome-devtools/network/?utm_source=devtools&utm_campaign=2019Q1).

Just as an example, click on a campaign, and in the DevTools, click on the inspect button. Then on the page, right click and click on 'View Page Source' to have a look at the source code.

The sources tab shows the raw state that the data came into the Web browser.

For more details and workflows, please see [this](https://developers.google.com/web/tools/chrome-devtools/open) page. 

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

XPath stands for XML Path Language and it uses 'path-like' syntax to identify and navigate nodes in an XML document. The term [node](https://en.wikipedia.org/wiki/Node_(computer_science)) can be used as a generic word that represents any object in the [DOM](https://en.wikipedia.org/wiki/Document_Object_Model) heirarchy. XPath expressions can be used in Python. The path expressions to select nodes or node-sets in an XML document look simmilar to path expressions used with traditional computer file systems:

<p align="center"><img src="/assets/img/indiegogo_XPath_folder_example.png" width="350" height="75"></p>

For example, given source XML that contains:

<img src="/assets/img/indiegogo_XPath_leters_ex.png" width="250" height="75">
  
The simplest form XPath would take would be:
/A/B/C

When we select our XPath query, we get back one or more nodes (e.g., `//html/body/h1`). The way that we separate the nodes in our path tells the selector what position in the document to look for.
-   The double "//" (forward slash) means anywhere from the top of the XML tree down (i.e., seach the entire XML document). When  "//"  separates steps in an expression (you can put  "//"  wherever a  "/"  occurs), it means search all descendants from the last step.
-   When a single "/" (forward slash) is the first symbol of the expression, it is telling the XPath processor to start the search at the top-most element. The single forward slash represents the document info item--also know as the root.  When  "/" is within an XPath expression, the subsequent named "steps" in the path represent children. 

If you want a second step (or child), specify that with a numerator inside of square brackets, for example, if there are two h1's in the body, and you want the child from the second h1, type `//html/body/h1[2]`.

There are other types of nodes, called attributes, that can also be used. Each node can have an id, which are generally unique in the XML document. Styling using CSS, uses the class identifier. You can also query attributes, which can be done using the "@" symbol. The XPaths can also be combined, compared, and etc. An entire node can be selected, or the HTML inside a node can be selected. The text or the value of a particular attribute inside a note can also be selected. A condition can be used (e.g., contains). Several combinations can be done to select something using XPath.

There are several great overviews that go into a lot of detail, such as those found at [scrapy.org](https://docs.scrapy.org/en/xpath-tutorial/topics/xpath-tutorial.html), [tutorialspoint](https://www.tutorialspoint.com/xpath/xpath_expression.htm), [Wikipedia](https://en.wikipedia.org/wiki/XPath), and [w3schools](https://www.w3schools.com/xml/xpath_intro.asp).

### Create a spider
Scrapy uses spiders, which are classes that you define, to scrape information from a website (or several websites). They need to subclass `scrapy.Spider` and define initial requests to make.

In the demo_scrapy/spiders directory in your project, make a file called (for example) `demo1_spider.py`. Save the code below within this file:

    import scrapy

    class CrowdfundSpider(scrapy.Spider): # Use the Spider class provided by Scrapy and make a subclass out of it called CrowdfundSpider
        name = "indiegogo_audio" # This is the name of the spider, which will be used to run the spider 
        start_urls = ["https://www.indiegogo.com/explore/audio?project_type=campaign&project_timing=all&sort=trending"] # Giving the scraper a single URL to start from

In the above code, two attributes are defined, which include:
-   `name`: the name of the spider. Unique names will help keep track of all of the spiders that are made.
-   `start_urls`: a list of URLs that you start to crawl from. We’ll start with one URL.

Enter the directory that your spider is in (e.g., demo_scrapy/spiders), and in the command line activate the environment that you'll be using (e.g., `conda activate environment_name`). Test out the scraper by typing the following command in the command line: `scrapy runspider demo1_spider.py`

Something similar to the following will be the output (note that some of the output from the command line has been removed, for readability purposes):

    2019-08-30 15:17:38 [scrapy.utils.log] INFO: Scrapy 1.7.3 started (bot: demo_scrapy)
    2019-08-30 15:17:38 [scrapy.utils.log] INFO: Versions: lxml 4.4.1.0, libxml2 2.9.9, cssselect 1.1.0, parsel 1.5.1, w3lib 1.20.0, Twisted 19.7.0, Python 3.7.3 | packaged by conda-forge | (default, Jul  1 2019, 14:38:56) - [Clang 4.0.1 (tags/RELEASE_401/final)], pyOpenSSL 19.0.0 (OpenSSL 1.1.1c  28 May 2019), cryptography 2.7, Platform Darwin-14.5.0-x86_64-i386-64bit
    2019-08-30 15:17:38 [scrapy.middleware] INFO: Enabled extensions:
    ['scrapy.extensions.corestats.CoreStats',
    'scrapy.extensions.telnet.TelnetConsole',
    'scrapy.extensions.memusage.MemoryUsage',
    'scrapy.extensions.logstats.LogStats']
    2019-08-30 15:17:39 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://www.indiegogo.com/robots.txt> (referer: None)
    2019-08-30 15:17:39 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://www.indiegogo.com/explore/audio?project_type=campaign&project_timing=all&sort=trending> (referer: None)
    2019-08-30 15:17:39 [scrapy.core.scraper] ERROR: Spider error processing <GET https://www.indiegogo.com/explore/audio?project_type=campaign&project_timing=all&sort=trending> (referer: None)
    Traceback (most recent call last):
    File "/anaconda3/envs/scenv/lib/python3.7/site-packages/twisted/internet/defer.py", line 654, in _runCallbacks
        current.result = callback(current.result, *args, **kw)
    File "/anaconda3/envs/scenv/lib/python3.7/site-packages/scrapy/spiders/__init__.py", line 80, in parse
        raise NotImplementedError('{}.parse callback is not defined'.format(self.__class__.__name__))
    NotImplementedError: CrowdfundSpider.parse callback is not defined
    2019-08-30 15:17:39 [scrapy.core.engine] INFO: Closing spider (finished)
    2019-08-30 15:17:39 [scrapy.statscollectors] INFO: Dumping Scrapy stats:
    {'downloader/request_bytes': 510,
    'downloader/request_count': 2,
    'downloader/request_method_count/GET': 2,
    'downloader/response_bytes': 9976,
    'downloader/response_count': 2,
    'downloader/response_status_count/200': 2,
    'elapsed_time_seconds': 0.967528,
    'finish_reason': 'finished',
    'finish_time': datetime.datetime(2019, 8, 30, 14, 17, 39, 851773),
    'log_count/DEBUG': 2,
    'log_count/ERROR': 1,
    'log_count/INFO': 10,
    'memusage/max': 46247936,
    'memusage/startup': 46247936,
    'response_received_count': 2,
    'robotstxt/request_count': 1,
    'robotstxt/response_count': 1,
    'robotstxt/response_status_count/200': 1,
    'scheduler/dequeued': 1,
    'scheduler/dequeued/memory': 1,
    'scheduler/enqueued': 1,
    'scheduler/enqueued/memory': 1,
    'spider_exceptions/NotImplementedError': 1,
    'start_time': datetime.datetime(2019, 8, 30, 14, 17, 38, 884245)}
    2019-08-30 15:17:39 [scrapy.core.engine] INFO: Spider closed (finished)

The scraper started and loaded the necessary components and extensions to read data from URLs. It used the URL that was provided in the start_urls list and grabbed the HTML. You can see a NotImplementedError('{}.parse callback is not defined') was raised because. A `parse` method was not written yet, so it passed the HTML to the `parse` method, which doesn't do anything by default, thus the spider finished without doing anything yet.



