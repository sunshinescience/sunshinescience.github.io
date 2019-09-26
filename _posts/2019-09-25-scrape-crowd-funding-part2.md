---
layout: post
title: "Creating a data set via Web Scraping Fundrazr with Python: Part 2 - expanding the data set"
---

In [part 1](https://sunshinescience.github.io/2019/08/24/scrape-crowd-funding_3.html) of this tutorial, we covered the following steps to create a data set via web scraping:

-   Start a project directory and create a [virtual environment](https://sunshinescience.github.io/2019/08/08/create-python-env.html)
-   Install scrapy, for example, in your project directory, type in the command line `conda install -c conda-forge scrapy`, followed by typing `y`
-   Create a new project within that directory. Type in the command line `scrapy startproject demo_scrapy`
-   Inspect the target website. Open Developer Tools in your browser or right click on the page and click inspect (or inspect element).
-   Use XPath to direct the scraper to a specific part of the HTML on the page.
-   Create a spider, to scrape information from a website (or several websites). Subclass `scrapy.Spider` and define initial requests to make.
-   Use Scrapy shell to get the correct XPath expressions to include in your spider. Step 1: Activate the environment - within the project folder, type in the command line `conda activate environment_name`. Step 2: Enter the Scrapy shell - type in the command line `scrapy shell`. Step 3: Use the fetch function to fetch the URL - type in the command line `fetch("URL")`. Step 4: Type the XPath selector to extract the data in Scrapy shell. Insert the needed XPath selectors to the spider.
-   Use the spider to crawl the page(s). Open up a new terminal, activate the environment, change directory into the spiders folder, and type in the command line `scrapy crawl spider_name`. 
-   Save the extracted data to a file. For example, type in the command line `scrapy crawl spider_name -o file_name.csv`. Now, the data set will be saved.

This part of the tutorial aims to expand on the [data set](https://github.com/sunshinescience/scrape_fund_raisn/blob/master/demo_scrapy/demo_scrapy/spiders/fundrazr_items.csv) made in Part 1. 

### Scrapy architecture
Scrapy provides the [Item](https://doc.scrapy.org/en/1.0/topics/items.html) class to store crawled data. Item objects are containers that are used to collect the scraped data. And they provide an API similar to dictionaries, with a syntax for declaring available fields. One of the reasons to use data points defined in Items would be to get the data in a file in some specific order. Let's have a look at our example to see why this might be needed.

Let's change the spider from [Part 1](https://github.com/sunshinescience/scrape_fund_raisn/blob/master/demo_scrapy/demo_scrapy/spiders/demo1_spider.py) to include more start URLs. The first page of each category could be added as a list under start_urls. Let's inspect the [page](https://fundrazr.com/find) and have a look at the code and use an XPath expression to get the first page URL for each category. 

<img src="/assets/img/fundrazr_categories.png">

In the Scrapy shell, type `response.xpath('//a[starts-with(@href, "https://fundrazr.com/find?category")]/@href').extract()`. This would give you a list of start URLs to add into the spider. The spider code (saved as demo2_spider_extended.py in this demonstration) is as follows:

    import scrapy

    class CrowdfundSpider(scrapy.Spider):
        name = "fundrazr_campaigns2"
        allowed_domains = ["fundrazr.com"]
        start_urls = [
                    'https://fundrazr.com/find?category=Accidents',
                    'https://fundrazr.com/find?category=Alumni',
                    'https://fundrazr.com/find?category=Animals',
                    'https://fundrazr.com/find?category=Arts',
                    'https://fundrazr.com/find?category=Business',
                    'https://fundrazr.com/find?category=Celebrations',
                    'https://fundrazr.com/find?category=Community',
                    'https://fundrazr.com/find?category=Education',
                    'https://fundrazr.com/find?category=Faith',
                    'https://fundrazr.com/find?category=Family',
                    'https://fundrazr.com/find?category=Health',
                    'https://fundrazr.com/find?category=Legal',
                    'https://fundrazr.com/find?category=Memorials',
                    'https://fundrazr.com/find?category=Non-profits',
                    'https://fundrazr.com/find?category=Politics',
                    'https://fundrazr.com/find?category=Sports',
                    'https://fundrazr.com/find?category=Travel',
                    'https://fundrazr.com/find?category=Veterans',
                    'https://fundrazr.com/find?category=Accidents',
                    'https://fundrazr.com/find?category=Alumni',
                    'https://fundrazr.com/find?category=Animals',
                    'https://fundrazr.com/find?category=Arts',
                    'https://fundrazr.com/find?category=Business',
                    'https://fundrazr.com/find?category=Celebrations',
                    'https://fundrazr.com/find?category=Community',
                    'https://fundrazr.com/find?category=Education',
                    'https://fundrazr.com/find?category=Faith',
                    'https://fundrazr.com/find?category=Family',
                    'https://fundrazr.com/find?category=Health',
                    'https://fundrazr.com/find?category=Legal',
                    'https://fundrazr.com/find?category=Memorials',
                    'https://fundrazr.com/find?category=Non-profits',
                    'https://fundrazr.com/find?category=Politics',
                    'https://fundrazr.com/find?category=Sports',
                    'https://fundrazr.com/find?category=Travel',
                    'https://fundrazr.com/find?category=Veterans'
                    ]

        def parse(self, response):
            category = response.xpath('//li[@class="active"]//a[starts-with(@href, "https://fundrazr.com/find?category=")]/text()').extract()
            widget_tall = response.xpath('//*[@class="widget tall"]')   

            for campaign in widget_tall:
                campaign_message = campaign.xpath('.//@data-message').extract() # Use extract_first() to get only the first campaign
                owner_name = campaign.xpath('.//@data-ownername').extract()
                location = campaign.xpath('.//p[@class="location"]/text()').extract()
                campaign_title = campaign.xpath('.//h2//a[starts-with(@href, "//fund")]/text()').extract()   
                
                currency_symbol = campaign.xpath('.//div[@class="clearfix stats-entries"]/div[1]/p[@class="stats-value"]/span/text()').extract()
                amount_raised = campaign.xpath('.//div[@class="clearfix stats-entries"]/div[1]/p[@class="stats-value"]/text()').extract()
                
                duration_running = campaign.xpath('.//div[@class="clearfix stats-entries"]/div[2]/p[@class="stats-value"]/text()').extract()
                duration_label = campaign.xpath('.//div[@class="clearfix stats-entries"]/div[2]/p[@class="stats-label"]/text()').extract()

                url = []
                for href in campaign.xpath('.//@data-campaignurl'):
                    base_url = "https:" + href.extract()
                    url.append(base_url)

                yield{'Category': category, 'Campaign title': campaign_title, 'Owner name' : owner_name, 'Location': location, 'Currency symbol': currency_symbol, 'Amount raised': amount_raised, 'Duration running': duration_running, 'Duration running label': duration_label, 'URL': url, 'Campaign description': campaign_message}

            next_page_url = response.xpath('//*[@class="next"]/a/@href').extract_first()
            absolute_next_page_url = response.urljoin(next_page_url)
            yield scrapy.Request(absolute_next_page_url)

If you have saved the spider above in a file, type in the command line `scrapy crawl fundrazr_campaigns2 -o fundrazr_items_extended.csv` to save the new data set to a file called 'fundrazr_items_extended.csv'. The updated data set extracts data from each of the categories on the site. However, there may be duplicates. The first page of each category is done first, and then the spider crawls to the next page of each category and so on. The categories are not all in order in the first column of the data set. If one wanted to have the columns set in a specific order in a .csv file, we would need to use Scrapy Item. The main goal in web scraping is to extract *structured data* from web pages. Therefore, to get the data in some specific order (i.e., *structure* the data) in a file, we would need to use [Items](https://doc.scrapy.org/en/1.0/topics/items.html).

Let's start adding to the items.py file in the project directory (in this demonstration the directory is called demo_scrapy). Let's start by adding to the class DemoScrapyItem:

    class DemoScrapyItem(scrapy.Item):
        # define the fields for your item here like:
        # name = scrapy.Field()
        campaign_message = scrapy.Field()
        owner_name = scrapy.Field()

And, we'll make a demo2_spider_architecture.py file, and import two modules. That is, ItemLoader from scrapy.loader and a local module. So, from the project directory (here called demo_scrapy) we need to get into items.py and import the class (called DemoScrapyItem) from this module. Let's define ItemLoader as a variable, which would be the class that we have defined in items.py and response will equal response (meaning the start URL). This spider now has the following code:

    import scrapy
    from scrapy.loader import ItemLoader
    from demo_scrapy.items import DemoScrapyItem

    class CrowdfundSpider(scrapy.Spider):
        name = 'fundrazr_campaigns3'
        allowed_domains = ["fundrazr.com"]
        start_urls = [
                    'https://fundrazr.com/find?category=Accidents'
                    ]

        def parse(self, response):
            l = ItemLoader(item=DemoScrapyItem(), repsonse=response)
            campaign_message = response.xpath('.//@data-message').extract() 
            owner_name = response.xpath('.//@data-ownername').extract()

            l.add_value('campaign_message', campaign_message)
            l.add_value('owner_name', owner_name)

            return l.load_item()

Save this file and open up a terminal, enter your project directory and activate the environment. Type in the command line `scrapy crawl fundrazr_campaigns3`.

The next file that could be used is pipelines.py, which would be used when receiving an item and performing an action on the item (e.g., if you want to do a calculation on the item, or if you want to change the text to upper case). And it also considers whether the item should continue through the pipeline or if it should be dropped and not processed any further.
        
