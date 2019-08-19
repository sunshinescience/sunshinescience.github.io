---
layout: post
title: "Creating a data set via Web Scraping with Python - in progress"
---

<img src="/assets/img/freestock_56437381.jpg">
Photo from [https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381](https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381)

[Web scraping](https://en.wikipedia.org/wiki/Web_scraping) is a technique that is used to extract data from websites. Sometimes it can be beneficial to gather and copy data from the web. But, if there isn't a direct link to download it, web scraping can be performed to obtain the data. For this demonstration, data from one crowd funding website (called Indiegogo) will be gathered, organized, and cleaned. The main goal of this demonstration is to obtain the unstructured data via web scraping and build a structured data set. Some additional sites might be added later on, which include those mentioned in [this](https://www.investopedia.com/articles/personal-finance/091415/8-best-alternatives-kickstarter.asp) article. 

It appears that there are two ways to accomplish this goal. [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) is a user-friendly Python library that can be used to parse HTML and XML files. [Scrapy](https://docs.scrapy.org/en/latest/) is a high-level web crawling and web scraping framework. Here, scrapy has been chosen to scrape the website. 

#### Installing scrapy
A scrapy tutorial can be found in the documentation [here](https://doc.scrapy.org/en/latest/intro/tutorial.html). It is recommended that one should install scrapy within a “virtual environment” ([virtualenv](https://virtualenv.pypa.io/en/latest/)). A virtualenv is an environment with its own installation directories, which doesn’t share libraries with other virtualenv environments (and optionally doesn’t access the globally installed libraries). 



