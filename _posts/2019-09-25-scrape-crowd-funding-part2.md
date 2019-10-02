---
layout: post
title: "Web Scraping Fundrazr with Python: Part 2 - expanding the data set"
---

<p align="center"><img src="/assets/img/fundrazr_part_2_blog.png" width="500" height="400"></p>
Photo modified from [https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381](https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381)

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

In the Scrapy shell, type `response.xpath('//a[starts-with(@href, "https://fundrazr.com/find?category")]/@href').extract()`. This will give you a list of category start URLs to add into the spider. The spider code (saved as demo2_spider_extended.py in this demonstration) is as follows:

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

If you have saved the spider above in a file, type in the command line `scrapy crawl fundrazr_campaigns2 -o fundrazr_items_extended.csv` to save the new data set to a file called 'fundrazr_items_extended.csv'. The updated data set extracts data from each of the categories on the site. The first page of each category is done first, and then the spider crawls to the next page of each category and so on. Note that the categories are not all in order in the first column of the data set. And there may be duplicates of some of the campaigns (this would have to be checked, but data cleaning is for another post). 

### Create a spider to scrape individual campaigns

The main goal in web scraping is to extract *structured data* from web pages. Therefore, to get the data in some specific order (i.e., *structure* the data) in a file, we would need to use Scrapy [Item](https://doc.scrapy.org/en/1.0/topics/items.html).

Let's start adding to the items.py file in the project directory (in this demonstration the directory is called demo_scrapy). Let's start by adding to the class DemoScrapyItem:

    class DemoScrapyItem(scrapy.Item):
        # define the fields for your item here like:
        # name = scrapy.Field()
        campaign_message = scrapy.Field()
        owner_name = scrapy.Field()

And, we'll make a demo2_spider.py file, and import two modules. That is, ItemLoader from scrapy.loader and a local module. So, from the project directory (here called demo_scrapy) we need to get into items.py and import the class (called DemoScrapyItem) from this module. Let's define ItemLoader as a variable, which would be the class that we have defined in items.py and response will equal response (meaning the start URL). This spider now has the following code:

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

Save this file and open up a terminal, enter your project directory and activate the environment. Type in the command line `scrapy crawl fundrazr_campaigns3`. Currently, information from 53,459 campaigns have been extracted (email me if you would like a copy of that dataset).

The next file that could be used is pipelines.py, which would be used when receiving an item and performing an action on the item (e.g., if you want to do a calculation on the item, or if you want to change the text to upper case). And it also considers whether the item should continue through the pipeline or if it should be dropped and not processed any further.

In the settings.py file, you can activate several options. 

The data we already have, from Part 1 of this tutorial, is as follows:

-   Category
-   Campaign title
-   Owner name
-   Location
-   Currency symbol
-   Amount raised
-   Duration running
-   Duration running label
-   URL
-   Campaign description

Let's now get into individual campaign links and extract data from each one. We are going to continue working on the spider from the code above, and thus create a different data set. The aim is to additionally obtain the following data from the site:

-   Amount raised
-   Raised progress
-   Project goal
-   Duration running (the amount of time the campaign has been running)
-   Number of contributers
-   Time a project was launched
-   Day a project was launched
-   Month a project was launched
-   Day of the week the project was launched on

When attempting to extract the date and time that an example campaign was launched on, using `response.xpath('//span[@class="stats-label"]/a').extract_first()`, the XPath selector did not get the whole element (see image below). 

<p align="center"><img src="/assets/img/fundrazr_missing_element_part.png"></p>

Thus, instead of extracting 'data-original-title' (see red rectangle in the example image below), we will extract the timestamp and convert it using datetime. We'll use the expression `(response.xpath('//span[@class="stats-label"]/a/@data-timestamp').extract_first()).rstrip("0")` to extract the timestamp and remove trailing zeros.

<p align="center"><img src="/assets/img/fundrazr_campaign_launched.png"></p>

We will get the day of the week the project was launched on (called day_of_week_launched in the spider) using Pandas Timestamp.weekday() function, which returns the day of the week represented by the date in the given Timestamp object. Monday == 0 … Sunday == 6.

For one example campaing, the spider now consists of the following code:

    import scrapy
    from scrapy.loader import ItemLoader
    from demo_scrapy.items import DemoScrapyItem
    from datetime import datetime

    class CrowdfundSpider(scrapy.Spider):
        name = 'fundrazr_campaigns3'
        allowed_domains = ["fundrazr.com"]
        start_urls = [
                    'https://fundrazr.com/find?category=Accidents'
                    ]

        def parse_base_page(self, response):
            category = response.xpath('//li[@class="active"]//a[starts-with(@href, "https://fundrazr.com/find?category=")]/text()').extract()
            yield{'Category': category}

        def parse(self, response):
            for href in response.xpath('//a[@class="campaign-link"]/@href'):
                url = "https:" + href.extract()
                yield scrapy.Request(url, callback=self.parse_campaign)	

        def parse_campaign(self, response):
            url = response.xpath('//meta[@property="og:url"]/@content').extract_first()
            
            l = ItemLoader(item=DemoScrapyItem(), repsonse=response)

            # Getting the campaign description
            message = response.xpath("//div[contains(@id, 'full-story')]//text()").extract()
            campaign_msg = []
            for i in message:
                if len(i.strip()) > 0:
                    campaign_msg.append(i.strip())
            campaign_message = (''.join(campaign_msg))

            raised_progress = response.xpath('//span[@class="raised-progress"]/text()').extract_first()
            goal = ((response.xpath('//*[@id="campaign-stats"]/div[1]/span[3]/text()[2]').extract_first()).strip()).split(' ')[1]
            campaign_title = (response.xpath('//div[@id="campaign-title"]/text()').extract_first()).strip()
            currency_symbol = response.xpath('//span[@class="currency-symbol"]/text()').extract_first()
            currency_raised = response.xpath('//span[@class="amount-raised"]/text()').extract_first()
            duration_running = response.xpath('//span[@class="stat"]/text()').extract_first() 
            duration_running_label = (response.xpath('//*[@id="campaign-stats"]/div[3]/span/span[2]/text()').extract()[0]).strip()
            number_contributers = response.xpath('//span[@class="donation-count stat"]/text()').extract_first() 
            location = response.xpath('//span/a[@class="muted nowrap"]/text()').extract_first()
            owner_name = response.xpath('//span/a[@class="name subtle-link bold"]/text()').extract_first()
            timestamp = (response.xpath('//span[@class="stats-label"]/a/@data-timestamp').extract_first()).rstrip("0") # Getting the timestamp and removing trailing zeros
            datetime_object = datetime.fromtimestamp(int(timestamp))
            hour_launched = (str(datetime_object.hour))
            month_launched = (str(datetime_object.month))
            day_launched = (str(datetime_object.day))
            day_of_week_launched = (str(datetime_object.weekday()))

            l.add_value('campaign_message', campaign_message)
            l.add_value('raised_progress', raised_progress)
            l.add_value('goal', goal)
            l.add_value('campaign_title', campaign_title)
            l.add_value('currency_symbol', currency_symbol)
            l.add_value('currency_raised', currency_raised)
            l.add_value('duration_running', duration_running)
            l.add_value('duration_running_label', duration_running_label)
            l.add_value('number_contributers', number_contributers)
            l.add_value('location', location)
            l.add_value('owner_name', owner_name)
            #l.add_value('timestamp', timestamp)
            l.add_value('hour_launched', hour_launched)
            #l.add_value('datetime_object', datetime_object)
            l.add_value('month_launched', month_launched)
            l.add_value('day_launched', day_launched)
            l.add_value('day_of_week_launched', day_of_week_launched)
            l.add_value('url', url)

            return l.load_item()

And the items.py file consists of the folowing code:

    # -*- coding: utf-8 -*-

    # Define here the models for your scraped items
    #
    # See documentation in:
    # https://docs.scrapy.org/en/latest/topics/items.html

    import scrapy


    class DemoScrapyItem(scrapy.Item):
        campaign_message = scrapy.Field()
        #category = scrapy.Field()
        raised_progress = scrapy.Field()
        goal = scrapy.Field()
        campaign_title = scrapy.Field()
        currency_symbol = scrapy.Field()
        currency_raised = scrapy.Field()
        duration_running = scrapy.Field()
        duration_running_label = scrapy.Field()
        number_contributers = scrapy.Field()
        location = scrapy.Field()
        owner_name = scrapy.Field()
        hour_launched = scrapy.Field()
        month_launched = scrapy.Field()
        day_launched = scrapy.Field()
        day_of_week_launched = scrapy.Field()
        url = scrapy.Field()
        
The columns are now in alphabetical order, but let's change the column order to the order we want. We'll use the [`FEED_EXPORT_FIELDS`](https://doc.scrapy.org/en/latest/topics/feed-exports.html#std:setting-FEED_EXPORT_FIELDS) option in order to do this. Thus,in the settings.py file, add the following code:

    FEED_EXPORT_FIELDS = ["campaign_title", "campaign_message", "owner_name", "url",                                "raised_progress", "goal", "currency_symbol", 
                        "currency_raised", "duration_running", "duration_running_label", "number_contributers", "location", "month_launched", 
                        "day_launched", "hour_launched", "day_of_week_launched"]

Let's also extend the spider it to scrape information from all campaigns on the website. Some of the campaigns don't have a goal, so the spider skipped those campaigns. Therefore, we've added in a try clause so that we can get the campaign title, campaign message (i.e., description), owner name, and URL from those campaigns, which shows up in the .csv file. 

If you scroll to the bottom of the main [page](https://fundrazr.com/find?category=Accidents), you'll see a `next` tab to get to the [next page](https://fundrazr.com/find?category=Accidents&page=2) (e.g., see red square in the image below). 

<p align="center"><img src="/assets/img/fundrazr_next_page.png"></p>

We've added the code in the spider in order to get the next page:

    next_page_url = response.xpath('//*[@class="next"]/a/@href').extract_first()
    absolute_next_page_url = response.urljoin(next_page_url)
    yield scrapy.Request(absolute_next_page_url)

Now, the spider crawls all of the pages within the link provided in the start_urls list. This is the code for the spider:

    import scrapy
    from scrapy.loader import ItemLoader
    from demo_scrapy.items import DemoScrapyItem
    from datetime import datetime

    class CrowdfundSpider(scrapy.Spider):
        name = 'fundrazr_campaigns3'
        allowed_domains = ["fundrazr.com"]
        start_urls = [
                    'https://fundrazr.com/find?category=Accidents'
                    ]

        def parse(self, response):
            for href in response.xpath('//h2//a[@class="campaign-link"]/@href'):
                url = "https:" + href.extract()
                yield scrapy.Request(url, callback=self.parse_campaign)
            
            next_page_url = response.xpath('//*[@class="next"]/a/@href').extract_first()
            absolute_next_page_url = response.urljoin(next_page_url)
            yield scrapy.Request(absolute_next_page_url) # Scrapy uses requests to ask for a page and gets responses from the webserver. 
            
            # Here, we could add in another Request with a callback to follow another link (if, for example, there was a link in an individual campaign in this example). And the callback here could go to another function that would then parse that followed link.

        def parse_campaign(self, response):
            l = ItemLoader(item=DemoScrapyItem(), repsonse=response)

            # Getting the campaign description
            message = response.xpath("//div[contains(@id, 'full-story')]//text()").extract()
            campaign_msg = []
            for i in message:
                if len(i.strip()) > 0:
                    campaign_msg.append(i.strip())
            campaign_message = (''.join(campaign_msg))
            l.add_value('campaign_message', campaign_message)

            # Getting the campaign title
            campaign_title = (response.xpath('//div[@id="campaign-title"]/text()').extract_first()).strip()
            l.add_value('campaign_title', campaign_title)

            # Getting the owner name
            owner_name = response.xpath('//span/a[@class="name subtle-link bold"]/text()').extract_first()
            l.add_value('owner_name', owner_name)

            # Getting the url
            url = response.xpath('//meta[@property="og:url"]/@content').extract_first()
            l.add_value('url', url)

            # Getting the rest of the data from the campaigns
            try:
                raised_progress = response.xpath('//span[@class="raised-progress"]/text()').extract_first()
                goal = ((response.xpath('//*[@id="campaign-stats"]/div[1]/span[3]/text()[2]').extract_first()).strip()).split(' ')[1]
                currency_symbol = response.xpath('//span[@class="currency-symbol"]/text()').extract_first()
                currency_raised = response.xpath('//span[@class="amount-raised"]/text()').extract_first()
                duration_running = response.xpath('//span[@class="stat"]/text()').extract_first() 
                duration_running_label = (response.xpath('//*[@id="campaign-stats"]/div[3]/span/span[2]/text()').extract()[0]).strip()
                number_contributers = response.xpath('//span[@class="donation-count stat"]/text()').extract_first() 
                location = response.xpath('//span/a[@class="muted nowrap"]/text()').extract_first()
                timestamp = (response.xpath('//span[@class="stats-label"]/a/@data-timestamp').extract_first()).rstrip("0") # Getting the timestamp and removing trailing zeros
                datetime_object = datetime.fromtimestamp(int(timestamp))
                hour_launched = (str(datetime_object.hour))
                month_launched = (str(datetime_object.month))
                day_launched = (str(datetime_object.day))
                day_of_week_launched = (str(datetime_object.weekday()))
                
                l.add_value('raised_progress', raised_progress)
                l.add_value('goal', goal)
                l.add_value('currency_symbol', currency_symbol)
                l.add_value('currency_raised', currency_raised)
                l.add_value('duration_running', duration_running)
                l.add_value('duration_running_label', duration_running_label)
                l.add_value('number_contributers', number_contributers)
                l.add_value('location', location)
                #l.add_value('timestamp', timestamp)
                l.add_value('hour_launched', hour_launched)
                #l.add_value('datetime_object', datetime_object)
                l.add_value('month_launched', month_launched)
                l.add_value('day_launched', day_launched)
                l.add_value('day_of_week_launched', day_of_week_launched)
            except:
                pass

            return l.load_item()

If you were to save this spider in a .py file, open up a terminal and in your project directory, type: `scrapy crawl fundrazr_campaigns3 -o file_name.csv` in order to get the .csv file of the data. Add more to the start_urls list in order to scrape the entire website if needed. 

### Final thoughts
We have extended the web scraping done for a website from Part 1 of this tutorial. We created a data set from all of the individual campaigns from a crowdfunding website, in Python using Scrapy. This provides an example of how to scrape an entire website. The data set would need to be assessed and cleaned before it could be used for analysis. If the website were to change, the code in the spider would also need to change, thus the code could be improved a little bit to help with this, but that would be for another post. 

<center>Happy coding!<center>

<center>.       .       .<center>