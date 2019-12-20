# Web_Scraping
Scrape the data and news titles from websites

## Problem
In this semester, we have acuquired loads of tools for wrangling data in python. Among them, web scraping is one of the most interesting topic, not just because it is useful for collecting data from websites but also by learning web scraping, we also acquired learn something new like HTML structure.

Here is the code for the project:

The first piece code is to scrape the sport news titles from Tencent Sport 

## Code1
```python
import requests
import pandas as pd 
from bs4 import BeautifulSoup

# set tup the empty lists as containers
title = []
link = [] 

# locate the targeted url
url = "https://sports.qq.com/"
data = requests.get(url)
soup = BeautifulSoup(data.text)

# after examining html structure of the website, all news titles are stored in the section of "div.scr-news" 
data1 = soup.select("div.scr-news")

for i in data1[0].select("li"):
    title.append(i.get_text())
    
for i in data1[0].select("li a[href]"):
    link.append(i.get('href'))

df_1 = pd.DataFrame({'Title':title,
                   'Link':link}                        
)

# store it as dataframe for data manipulation
df_1

```
## result 1
Here are the results after running the python scripts
![Result](https://github.com/kwlwman2/Web_Scraping/blob/master/Screenshot%202019-12-20%20at%2015.22.45.png?raw=true)
