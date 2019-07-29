---
layout: post
title: "Web Scraping with Python - in progress"
---

<img src="/assets/img/freestock_56437381.jpg">
Photo from [https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381](https://www.freestock.com/free-photos/3d-laptop-computers-networking-isolated-white-56437381)

[Web scraping](https://en.wikipedia.org/wiki/Web_scraping) is a technique that is used to extract data from websites. Sometimes it can be beneficial to gather data from the web. But, if there isn't a direct link to download it, web scraping can be performed to obtain the data. For this demonstration, data will be gathered and copied from crowd funding websites, such as those mentioned in [this](https://www.investopedia.com/articles/personal-finance/091415/8-best-alternatives-kickstarter.asp) article. The goal of this demonstration is to obtain the unstructured data via web scraping and build a dataset.  

It appears that there are two ways to accomplish this goal. BeautifulSoup is a user-friendly library that can be used to parse HTML and XML files. Scrapy is a high-level web crawling and web scraping framework. 

A subsequent blog post will include how to manipulate and clean this dataset using Python's Pandas library.