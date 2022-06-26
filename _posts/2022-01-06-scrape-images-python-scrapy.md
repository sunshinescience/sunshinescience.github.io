---
layout: post
title: "How to scrape images and videos using Scrapy - in progress"
---

<p align="center"><img src="/assets/img/altumcodecompunsplash.jpg" width="500" height="650"></p>
<p align="center">Photo by [AltumCode](https://unsplash.com/photos/oZ61KFUQsus) on [Unsplash](https://unsplash.com/)</p>


[Web scraping](https://en.wikipedia.org/wiki/Web_scraping) can be used to extract data from websites. Here, we will use [Scrapy](https://docs.scrapy.org/en/0.24/index.html) to extract images from instagram.

Using Python, this could likely be achieved in a variety of ways. [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) is a user-friendly Python library that can be used to parse HTML and XML files. [Scrapy](https://docs.scrapy.org/en/latest/) is a high-level web crawling and web scraping framework. And [Selenium](https://www.selenium.dev/) can automate web browsers interaction from Python. It can be really useful when you need to automate something on the web (e.g., performing tasks such as clicking buttons, entering information in forms, or taking a screenshot). Scrapy has been chosen to scrape the images from the website as the data size could potentially be large and it is faster at crawling. 

### Getting started
It is assumed that [Python](https://www.python.org/) 2.7 and [pip](https://pip.pypa.io/en/latest/installing/) are already installed. 

Install Scrapy using pip:
`pip install scrapy`

### Create a project
Set up a new scrapy project. Enter the directory where you’d like to store your code and then run the following command in the terminal:

`scrapy startproject imgscrape`

You can start your first spider with:
    cd imgscrape


### Defining the Item

An item is a container that will be loaded with the scraped data. Items work similarly to python dicts. They can be declared by creating a scrapy.Item class and also defining the attributes as scrapy.Field objects.

### Inspect the target web page

### Obtaining the data

#### Getting the images

Check out this video: https://www.youtube.com/watch?v=jMMErSuEJ2g

To scrape the images, we will just perform a google search for golf swing images, which can be found [here](https://www.google.com/search?q=golf+swing+images&client=firefox-b-e&sxsrf=ALeKk02GLeeXfjkKI7QpEHryzmSD-CnX2w:1609934027493&source=lnms&tbm=isch&sa=X&ved=2ahUKEwjg6cfOn4fuAhXUOcAKHUyrAoEQ_AUoAXoECBEQAw&biw=990&bih=768).

Or, for a test run, let's get some images from [here](https://www.gettyimages.co.uk/photos/golf-swing?license=rf&phrase=golf%20swing&sort=best).

img tag with a class name: .gallery-asset__thumb gallery-mosaic-asset__thumb
And, the src is located within that img tag



CSS selector example:
div.gallery-mosaic-asset:nth-child(1) > article:nth-child(1) > a:nth-child(2) > figure:nth-child(1) > img:nth-child(2)

XPath selector example:
/html/body/div[2]/section/div/main/section/div[3]/div/div[3]/div/div[2]/div/div[1]/article/a/figure/img


We need to define the spider class, we will call it 'GolfImgSpider'. And we need to subclass `scrapy.Spider` and define initial requests. See the below code for an example:

class GolfImgSpider

#### Getting the videos
To scrape the videos:
On instagram, the golf swings are shown as videos [here](https://www.instagram.com/explore/tags/golfswing/). Let's see if its possible to write a web scraper to download each video. And then we will write script to open up each video, play it and take screenshots of the video to create a database that is an image collection of those videos.


<center>Happy coding!<center>

<center>.           .           .<center>
