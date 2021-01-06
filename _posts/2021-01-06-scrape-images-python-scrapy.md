---
layout: post
title: "How to Webscrape Images using Scrapy - in progress"
---

<p align="center"><img src="/assets/img/altumcodecompunsplash.jpg" width="350" height="500"></p>
Photo by [AltumCode](https://unsplash.com/photos/oZ61KFUQsus) on [Unsplash](https://unsplash.com/)


[Web scraping](https://en.wikipedia.org/wiki/Web_scraping) can be used to extract data from websites. Here, we will use [Scrapy](https://docs.scrapy.org/en/0.24/index.html) to extract images from instagram.

Using Python, this could likely be achieved in a variety of ways. [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) is a user-friendly Python library that can be used to parse HTML and XML files. [Scrapy](https://docs.scrapy.org/en/latest/) is a high-level web crawling and web scraping framework. And [Selenium](https://www.selenium.dev/) can automate web browsers interaction from Python. It can be really useful when you need to automate something on the web (e.g., performing tasks such as clicking buttons, entering information in forms, or taking a screenshot). Scrapy has been chosen to scrape the images from the website as the data size could potentially be large and it is faster at crawling. 

### Getting started
It is assumed that you already have [Python](https://www.python.org/) 2.7 and [pip](https://pip.pypa.io/en/latest/installing/) installed, but if not, then please install them. 

Install Scrapy using pip:
`pip install scrapy`

### Create a project
Set up a new scrapy project. Enter the directory where you’d like to store your code and then run the following command in the terminal:

`scrapy startproject golfimg`

You can start your first spider with:
    cd golfimg


### Defining the Item

An item is a container that will be loaded with the scraped data. Items work similarly to python dicts. They can be declared by creating a scrapy.Item class and also defining the attributes as scrapy.Field objects.

#### Obtaining the data


<center>Happy coding!<center>

<center>.           .           .<center>
