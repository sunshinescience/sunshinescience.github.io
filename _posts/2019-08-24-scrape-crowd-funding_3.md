---
layout: post
title: "Creating a data set via Web Scraping Fundrazr with Python - in progress"
---

<img src="/assets/img/freestock_56437381.jpg">
Photo from [https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381](https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381)

[Web scraping](https://en.wikipedia.org/wiki/Web_scraping) is a technique that is used to extract data from websites. Sometimes it can be beneficial to gather and copy data from the web. But, if there isn't a direct link to download it, web scraping can be performed to obtain the data. Mostly everything that can be found online is scrape-able. Scraping works by sending a request to a server and it receives a response made up of the HTML, CSS, and etc. that make up that page. Then it finds the information that you want in that response code.

For this demonstration, data from one crowd funding website (called Fundrazr) will be gathered, organized, and cleaned. The main goal of this demonstration is to obtain the unstructured data via web scraping and build a structured data set. Some additional sites might be added later on, which include those mentioned in [this](https://www.investopedia.com/articles/personal-finance/091415/8-best-alternatives-kickstarter.asp) article. 

It appears that there are (at least) two ways to accomplish this goal using Python. [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) is a user-friendly Python library that can be used to parse HTML and XML files. [Scrapy](https://docs.scrapy.org/en/latest/) is a high-level web crawling and web scraping framework. Here, scrapy has been chosen to scrape the website. 

### Installing scrapy
It is recommended that one should install scrapy within a “virtual environment” ([virtualenv](https://virtualenv.pypa.io/en/latest/)). A virtualenv is an environment with its own installation directories, which doesn’t share libraries with other virtualenv environments (and optionally doesn’t access the globally installed libraries). The virtual environment installation documentation can be found [here](https://virtualenv.pypa.io/en/stable/installation/) and a user guide can be found [here](https://virtualenv.pypa.io/en/stable/userguide/). An example of how to set up a virtual environment on a mac can be found in [this](https://sunshinescience.github.io/2019/08/08/create-python-env.html) blog post.

A great scrapy tutorial can be found in the documentation [here](https://doc.scrapy.org/en/latest/intro/tutorial.html). To install Scrapy via the conda-forge channel, type in the terminal (mac/linux) or command line (windows):

    conda install -c conda-forge scrapy

Type `y` to proceed with necessary packages. 

### Scrapy commands
Type `scrapy` in the command line and some of the available Scrapy commands will be provided as an output:

    $ scrapy
    Scrapy 1.7.3 - project: demo_scrapy

    Usage:
    scrapy <command> [options] [args]

    Available commands:
    bench         Run quick benchmark test
    check         Check spider contracts
    crawl         Run a spider
    edit          Edit spider
    fetch         Fetch a URL using the Scrapy downloader
    genspider     Generate new spider using pre-defined templates
    list          List available spiders
    parse         Parse URL (using its spider) and print the results
    runspider     Run a self-contained spider (without creating a project)
    settings      Get settings values
    shell         Interactive scraping console
    startproject  Create new project
    version       Print Scrapy version
    view          Open URL in browser, as seen by Scrapy

    Use "scrapy <command> -h" to see more info about a command

### Create a new project
Navigate into a directory where you'd like to store your code (here the new project will be called 'demo_scrapy') and type:

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
Let's first inspect the website. Chrome DevTools is a set of web developer tools that is built directly into the Google Chrome browser. In Chrome's main menu, click in the upper right corner, and click on the 'More Tools' tab, and click on the 'Developer Tools' tab. This opens up the developer tools. 

 <img src="/assets/img/fundrazr_developer_tools.png">
 **Figure 1.** Open DevTools from Chrome's main menu

The tabs that we're interested in include:
-   **Elements:** allows us to inspect the DOM and see where things are and how they're positioned. 
-   **[Console](https://developers.google.com/web/tools/chrome-devtools/console/):** allows us to interact with the page, but its main uses are to view logged messages and run JavaScript. 
-   **Sources:** helps find the origin of the data. It shows the raw state that the data came into the Web browser. This can help you debug JavaScript code. 
-   **Network:** allow us to see where the data originates from a server perspective. This categorizes and displays detailed information about each related operation on the page.
-   **Application:** provides information about what is stored in the browser relative to the site

If you click on the elements tab in DevTools, and click on the inspect tool (see red square in the image below), you can move over the page and inspect different elements. 

<img src="/assets/img/fundrazr_elements_tab.png">

And if you click on something, the HTML markup that is displaying that selection gets highlighted. And you can see the HTML's markup path (e.g., in the red rectangle in the below image).

<img src="/assets/img/fundrazr_elemnts_path.png">

Right click on the HTML markup and select copy and copy XPath. If you paste that into the console, you can see what the XPath looks like. If you want to view the network activity that a page has, please see the tutorial found [here](https://developers.google.com/web/tools/chrome-devtools/network/?utm_source=devtools&utm_campaign=2019Q1).

To see the source code (as an example), click on a campaign, and in the DevTools, click on the inspect button. Then on the page, right click and then click on 'View Page Source' to have a look at the source code.

For more details and workflows, please see [this](https://developers.google.com/web/tools/chrome-devtools/open) page. 

The Web page appears to have the following structure:
-   A header image (and its link) is on each page.
-   The categories are shown, with the current page category highlighted.
-   Several campaigns are shown. Each comprises a similar format that contains an image, title, key information, amount of money raised, and the duration running.

### Get a list of start url's
start_urls is a list of the [URLs](https://en.wikipedia.org/wiki/URL) which the spider will *start* to crawl from. In order to get individual campaign links, we will use each element in this list of the URLs. 

On the website [www.fundrazr.com](https://fundrazr.com/find), the find campaigns tab is selected, followed by Accidents & Disasters. You can sort by *Trending* or *Ending soon* or *Newest*. Or, there are several categories to choose from. We'll start with Accidents & Disasters. The first URL in our list of start_urls is this: https://fundrazr.com/find?category=Accidents.

### Find individual campaign links
You can use your browser's [development tools](https://docs.scrapy.org/en/latest/topics/developer-tools.html) for web scraping. Here, we'll use Google Chrome, so right click on the individual campaign and click 'inspect' to see where the campaigns are located within the code (see image below).In firefox, right click on the web page and click 'inspect element.' Firefox then shows you the code that it is executing to generate that page. 

<p align="center"><img src="/assets/img/fundrazr_indiv_campaign_lrge.png"></p>

#### XPath
In this demonstration, [XPath](https://en.wikipedia.org/wiki/XPath) is used to direct our scraper to a specific part of the HTML. XPath stands for XML Path Language and it uses 'path-like' syntax to identify and navigate nodes in an XML document. The term [node](https://en.wikipedia.org/wiki/Node_(computer_science)) can be used as a generic word that represents any object in the [DOM](https://en.wikipedia.org/wiki/Document_Object_Model) heirarchy. XPath expressions can be used in Python. The path expressions to select nodes or node-sets in an XML document look simmilar to path expressions used with traditional computer file systems:

<p align="center"><img src="/assets/img/XPath_folder_example.png" width="350" height="75"></p>

For example, given source XML that contains:

<img src="/assets/img/XPath_leters_ex.png" width="275" height="75">
  
The simplest form XPath would take would be:
/A/B/C

When we select our XPath query, we get back one or more nodes (e.g., `//html/body/h1`). The way that we separate the nodes in our path tells the selector what position in the document to look for.
-   The double "//" (forward slash) means anywhere from the top of the XML tree down (i.e., seach the entire XML document). When  "//"  separates steps in an expression (you can put  "//"  wherever a  "/"  occurs), it means search all descendants from the last step.
-   When a single "/" (forward slash) is the first symbol of the expression, it is telling the XPath processor to start the search at the top-most element. The single forward slash represents the document info item--also know as the root.  When  "/" is within an XPath expression, the subsequent named "steps" in the path represent children. 

If you want a second step (or child), specify that with a numerator inside of square brackets, for example, if there are two h1's in the body, and you want the child from the second h1, type `//html/body/h1[2]`.

There are other types of nodes, called attributes, that can also be used. Each node can have an id, which are generally unique in the XML document. Styling using CSS, uses the class identifier. You can also query attributes, which can be done using the "@" symbol. The XPaths can also be combined, compared, and etc. An entire node can be selected, or the HTML inside a node can be selected. The text or the value of a particular attribute inside a note can also be selected. A condition can be used (e.g., contains). Several combinations can be done to select something using XPath.

There are several great overviews that go into a lot of detail, such as those found at [scrapy.org](https://docs.scrapy.org/en/xpath-tutorial/topics/xpath-tutorial.html), [tutorialspoint](https://www.tutorialspoint.com/xpath/xpath_expression.htm), [Wikipedia](https://en.wikipedia.org/wiki/XPath), and [w3schools](https://www.w3schools.com/xml/xpath_intro.asp), and a good blog  tutorial found [here](https://blog.scrapinghub.com/2016/10/27/an-introduction-to-xpath-with-examples#comments-listing).

Note that if you prefer using CSS, a detailed CSS reference can be found [here](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference) and some CSS selectors can be found [here](https://www.w3schools.com/cssref/css_selectors.asp). An interactive CSS cheatsheet can be found [here](https://htmlcheatsheet.com/css/). And to learn the basics of HTML, see [here](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML). 

### Create a spider
Scrapy uses spiders, which are classes that you define, to scrape information from a website (or several websites). They need to subclass `scrapy.Spider` and define initial requests to make.

In the demo_scrapy/spiders directory in your project, make a file called (for example) `demo1_spider.py`. Save the code below within this file:

    import scrapy

    class CrowdfundSpider(scrapy.Spider): # Use the Spider class provided by Scrapy and make a subclass out of it called CrowdfundSpider
        name = "indiegogo_audio" # This is the name of the spider, which will be used to run the spider 
        allowed_domains = ["https://fundrazr.com"]
        start_urls = ["https://fundrazr.com/find?category=Accidents"] # Giving the scraper a single URL to start from

In the above code, three attributes are defined, which include:
-   `name`: the name of the spider. Unique names will help keep track of all of the spiders that are made.
-   `allowed_domains`: = the allowed site.
-   `start_urls`: a list of URLs that you start to crawl from. We’ll start with one URL.

Enter the directory that your spider is in (e.g., demo_scrapy/spiders), and in the command line activate the environment that you'll be using (e.g., `conda activate environment_name`). Test out the scraper by typing the following command in the command line: `scrapy runspider demo1_spider.py`

Something similar to the following will be the output, but note that some of the output from the command line has been removed for readability:

    2019-09-16 10:46:49 [scrapy.utils.log] INFO: Scrapy 1.7.3 started (bot: demo_scrapy)
    2019-09-16 10:46:49 [scrapy.utils.log] INFO: Versions: lxml 4.4.1.0, libxml2 2.9.9, cssselect 1.1.0, parsel 1.5.1, w3lib 1.20.0, Twisted 19.7.0, Python 3.7.3 | packaged by conda-forge | (default, Jul  1 2019, 14:38:56) - [Clang 4.0.1 (tags/RELEASE_401/final)], pyOpenSSL 19.0.0 (OpenSSL 1.1.1c  28 May 2019), cryptography 2.7, Platform Darwin-14.5.0-x86_64-i386-64bit
    2019-09-16 10:46:49 [scrapy.crawler] INFO: Overridden settings: {'BOT_NAME': 'demo_scrapy', 'NEWSPIDER_MODULE': 'demo_scrapy.spiders', 'ROBOTSTXT_OBEY': True, 'SPIDER_LOADER_WARN_ONLY': True, 'SPIDER_MODULES': ['demo_scrapy.spiders']}
    2019-09-16 10:46:49 [scrapy.extensions.telnet] INFO: Telnet Password: 604074340d368054
    2019-09-16 10:46:49 [scrapy.middleware] INFO: Enabled extensions:
    2019-09-16 10:46:50 [scrapy.middleware] INFO: Enabled item pipelines:
    []
    2019-09-16 10:46:50 [scrapy.core.engine] INFO: Spider opened
    2019-09-16 10:46:50 [scrapy.extensions.logstats] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
    2019-09-16 10:46:50 [scrapy.extensions.telnet] INFO: Telnet console listening on 127.0.0.1:6023
    2019-09-16 10:46:50 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://fundrazr.com/robots.txt> (referer: None)
    2019-09-16 10:46:52 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://fundrazr.com/find?category=Accidents> (referer: None)
    2019-09-16 10:46:52 [scrapy.core.scraper] ERROR: Spider error processing <GET https://fundrazr.com/find?category=Accidents> (referer: None)
    Traceback (most recent call last):
    File "/anaconda3/envs/scenv/lib/python3.7/site-packages/twisted/internet/defer.py", line 654, in _runCallbacks
        current.result = callback(current.result, *args, **kw)
    File "/anaconda3/envs/scenv/lib/python3.7/site-packages/scrapy/spiders/__init__.py", line 80, in parse
        raise NotImplementedError('{}.parse callback is not defined'.format(self.__class__.__name__))
    NotImplementedError: CrowdfundSpider.parse callback is not defined
    2019-09-16 10:46:52 [scrapy.core.engine] INFO: Closing spider (finished)
    2019-09-16 10:46:52 [scrapy.statscollectors] INFO: Dumping Scrapy stats:
    2019-09-16 10:46:52 [scrapy.core.engine] INFO: Spider closed (finished)

The scraper started and loaded the necessary components and extensions to read data from URLs. It used the URL that was provided in the start_urls list and grabbed the HTML. A NotImplementedError('{}.parse callback is not defined') was raised. This is because a `parse` method was not written yet, so it passed the HTML to the `parse` method, which doesn't do anything by default, thus the spider finished without doing anything yet.

### Extract data from a Web page
Scraping the first page involves searching for the regions of the page that contains the data we want to extract. And, grabbing the data from each campaign by pulling the data out of the HTML tags. Scrapy extracts data via using selectors, either CSS or XPath expressions. CSS selectors are actually converted to XPath. XPath expressions not only navigate the structure, but they can also look at the content. 

#### How to scrape using the Scrapy shell
Step 1: Activate the environment
In a mac, open up a terminal from the utilities menu. Change directory in the command line (i.e., type `cd` in the command line until you're in your project folder) into the project folder and type in the command line `conda activate scenv`. 

Step 2: Enter the Scrapy shell 
Type the command `scrapy shell` in the command line.
Use the command `shelp()` if you need to print Shell help. Use the command `view(response) ` to view response in a browser.

Step 3: Use the fetch function to fetch the URL
Type in the command line `fetch("https://fundrazr.com/find?category=Accidents")`. The 200 in parentheses (following the words 'DEBUG: crawled' in the output) indicates that the response is successful. If its 200 or 300, then it should be good. The specified URL is also shown in the output following this.

Step 4: Type the XPath selector to extract the data
For example, type in the command line `response.xpath('/html/head/title').extract_first()`. The output should be:

    '<title>Raise money for Accidents, Crisis &amp; Emergencies - FundRazr</title>'

#### Adding to the spider defined above
For now, we'll be continuing with step 4 as mentioned above. Let's start using XPath selectors to find all of the campaigns on the page. For example, we would use the XPath selector `response.xpath('//h2//a[starts-with(@href, "//fund")]/text()').extract()` to extract the campaign titles from the HTML. You can see below the structure of where an individual campaign link is within the HTML.

<p align="center"><img src="/assets/img/fundrazr_indiv_campaign.png"></p>

Use  `response.xpath('//h2//a//@href').extract()` to extract the campaign links. The code in the spider below provides the campaign titles and the links for this page:

    import scrapy

    class CrowdfundSpider(scrapy.Spider): # Use the Spider class provided by Scrapy and make a subclass out of it called CrowdfundSpider
        name = "fundrazr_accidents" # This is the name of the spider, which will be used to run the spider when `scrapy crawl name_of_spider` is used
        start_urls = ["https://fundrazr.com/find?category=Accidents"]

    def parse(self, response):
        campaign_titles = response.xpath('//h2//a[starts-with(@href, "//fund")]/text()').extract( 
        urls = []
        for href in response.xpath('//h2//a//@href'):
            url = "https:" + href.extract()
            urls.append(url)

        yield {'Campaign titles': campaign_titles, 'URL': urls}


In inspecting the code, it can be seen that the campaigns start with a class called 'widget tall'. 

<img src="/assets/img/fundrazr_campaigns_class.png">

Type in the command line the following code: `response.xpath('//*[@class="widget tall"]')`, which will give as an output the selectors:

<img src="/assets/img/fundrazr_class_widgetTall.png">

Next, in the command line, type: `fundrazr_campaigns = response.xpath('//*[@class="widget tall"]')`. And then type: `campaign = fundrazr_campaigns[0]`, which will be the first campaign on the page:

    In [4]: campaign                                                                              
    Out[4]: <Selector xpath='//*[@class="widget tall"]' data='<div class="widget tall" data-campaignur'>

Type in the command line `campaign.extract()`, which would output everything from the first campaign. Now, we have a custom selector, and can type in the command line `campaign.xpath()` and use the XPath selector that we want for this first campaign on the page.  For example, to only get content from an a tag from this campaign (using a dot in front to use this custom selector), type : `campaign.xpath('.//a')` and you can see the a tags or HTML nodes found:

img <src="/assets/img/fundrazr_a_tags_nodes.png">

In the HTML, the campaign message is found within `data message` (see red rectangle in the image below):

img <src="/assets/img/fundrazr_data_message.png">

Type in the command line: `campaign.xpath('.//@data-message').extract_first()`, which will output the campaign message, for example, although the campaigns may be different in the near future, currently, this will output: 

    In [20]: campaign.xpath('.//@data-message').extract_first()                                   Out[20]: 'Ride to Give is a 501c3 (tax ID 46-2952297) public charity that turns athletic ability into fundraising power for families with children who are disabled, injured, or ill. All donations made here will go to our current and future causes. Thank you!'

We will call this above selector, the `campaign_message` variable. To get the campaign owner name, type in the command line: `campaign.xpath('.//@data-ownername').extract_first()` and assign it to the variable called `owner_name`. This will go in the spider:

    import scrapy

    class CrowdfundSpider(scrapy.Spider): # Use the Spider class provided by Scrapy and make a subclass out of it called CrowdfundSpider
        name = "fundrazr_campaigns" # This is the name of the spider, which will be used to run the spider when `scrapy crawl name_of_spider` is used
        start_urls = ["https://fundrazr.com/find?category=Accidents"]

    def parse(self, response):
    '''    
        campaign_titles = response.xpath('//h2//a[starts-with(@href, "//fund")]/text()').extract() 
        urls = []
        for href in response.xpath('//h2//a//@href'):
            url = "https:" + href.extract()
            urls.append(url)

        yield {'Campaign titles': campaign_titles, 'URL': urls}   
    '''
        widget_tall = response.xpath('//*[@class="widget tall"]')
        for campaign in widget_tall:
            campaign_message = campaign.xpath('.//@data-message').extract_first() 
            owner_name = campaign.xpath('.//@data-ownername').extract_first()

            print ('\n')
            print (campaign_message)
            print (owner_name)
            print ('\n')

Next, open up a new terminal, activate the environment, change directory into the spiders folder, and type in the command line `scrapy crawl fundrazr_campaigns`. You should see in the terminal the output for all of the campaign messages (each followed by their respective owner name).
  
In order to get the locations of the campaigns, use the following selector: `response.xpath('//p[@class="location"]/text()').extract()`. The image below shows some of the HTML code which is how the amount raised and duration running (see red rectangles) is within the document.

img <src="/assets/img/fundrazr_stats.png">

The selector to get the currency symbol and amount raised (note, use `extract()` rather than `extract_first()` in order to get these from all campaigns) is as follows:

    # Currency symbol
    response.xpath('//div[@class="clearfix stats-entries"]/div[1]/p[@class="stats-value"]/span/text()').extract_first()

    # Amount raised
    response.xpath('//div[@class="clearfix stats-entries"]/div[1]/p[@class="stats-value"]/text()').extract_first()

Combine the first two selectors above if needed.

In order to obtain the duration running, use:
    
    # Duration
    response.xpath('//div[@class="clearfix stats-entries"]/div[2]/p[@class="stats-value"]/text()').extract_first() 

    # Duration running label
    response.xpath('//div[@class="clearfix stats-entries"]/div[2]/p[@class="stats-label"]/text()').extract_first()

Now, we have the information from the campaigns for the first page. Let's get more campaigns. Scroll to the bottom of the [page](https://fundrazr.com/find?category=Accidents). Right click on next and click `inspect` from the drop down menu. You can see the href for the next page (red arrow in image below).

img <src="/assets/img/fundrazr_next_inspect.png">

While clicking next and going to additional pages, it is observed that the href="find?category=Accidents&amp;page=*n*" where *n* here is a page number that increases as you click through the pages. To get the absolute URLs, use `response.urljoin` the spider we are building should now look like this:

    import scrapy


    class CrowdfundSpider(scrapy.Spider):
        name = "fundrazr_campaigns"
        allowed_domains = ["fundrazr.com"]
        start_urls = [
            'https://fundrazr.com/find?category=Accidents'
        ]

        def parse(self, response):
            widget_tall = response.xpath('//*[@class="widget tall"]')
            for campaign in widget_tall:
                campaign_message = campaign.xpath('.//@data-message').extract() # Use extract_first() to get only the first campaign
                owner_name = campaign.xpath('.//@data-ownername').extract()

                print ('\n')
                print (campaign_message)
                print ('\n')
                print (owner_name)
                print ('\n')

            next_page_url = response.xpath('//*[@class="next"]/a/@href').extract_first()
            absolute_next_page_url = response.urljoin(next_page_url)
            yield scrapy.Request(absolute_next_page_url)

If you run the spider again (by typing in the command line, within the folder spiders `scrapy crawl fundrazr_campaigns`), you will get all of the campaign descriptions, followed by each corresponding owner name. Thus, we have now scraped the entire site under the category Accidents & Disasters. Let's replace the Python print functions in the spider above with a yield function (using a dictionary), such as `yield{'Campaign message': campaign_message, 'Owner name' : owner_name}`. In using the yield function, some things get printed out afterwards, such as 'item scraped count', which let's us know how many items were scraped. Here (see image below), it shows 1289, thus we have scraped 1289 campaigns.

img <src="/assets/img/fundrazr_scrape_stats.png">

In order to save these data points to a file, type one of the following into the command line: `scrapy crawl fundrazr_campaigns -o fundrazr_items.csv`, `scrapy crawl fundrazr_campaigns -o fundrazr_items.json`, `scrapy crawl fundrazr_campaigns -o fundrazr_items.xml`. Remember, fundrazr_campaigns is the name of the spider. And fundrazr_items.csv would be the name of the file you want to make. The file format that you choose to put your data into would need to be made one after another. For example, first we made a .csv file:

img <src="/assets/img/fundrazr_first_csv_file.png">

Next, let's expand our spider to include the above selectors. And then we'll go into individual campaigns to get information from them as well. 


