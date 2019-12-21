# Web_Scraping
Scrape the data and news titles from websites

## Problem
In this semester, we have acuquired loads of tools for wrangling data in python. Among them, web scraping is one of the most interesting topic, not just because it is useful for collecting data from websites but also by learning web scraping, we also acquired learn something new like HTML structure.

Here is the code for the project:

## Code1 - The first piece of code is to scrape the sport news titles from Tencent Sport 

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


## Code2 - The second piece of code is to scrape the information about data scientist internship from www.indeed.com

```python
import requests 
from bs4 import BeautifulSoup

title = []
link = []
company = []
location = []

page = list(range(10,60,10))

for i in page:
    
    url = 'https://www.indeed.com/jobs?q=data+analyst&l=California&start='
    address = requests.get(url)
    soup = BeautifulSoup(address.text)
    data= soup.find_all('div',{'class':'title'})
    
    # title
    for i in range(0,len(data)):
        title.append(data[i].select('div.title a')[0].get('title'))   
    # link
    for i in range(0,len(data)):
        link.append('https://www.indeed.com'+data[i].select('div.title a[href]')[0].get('href'))
    
    # company
    data3 = soup.select('div.sjcl')
    for i in range(0,len(data3)):
        company.append(data3[i].select('div')[0].get_text().replace('\n',''))
    
    # location:
    for i in range(0,len(data3)):
        location.append(data3[i].select('div')[1].get('data-rc-loc')) 
        
    url = 'https://www.indeed.com/jobs?q=data+analyst&l=California&start={}'.format(str(i))
    
df = pd.DataFrame(
    {
        'Title':title,
        "Company":company,
        "Location":location,
        "Link":link
    }
) 

display(df)

```

Here are the results after running the code
![Result](https://github.com/kwlwman2/Web_Scraping/blob/master/results2.png)

## Code3 - The third piece of code is to scrape the data about rent properties from https://www.28hse.com/

```python
import re
import requests
import numpy as np 
import pandas as pd
from bs4 import BeautifulSoup

pages = list(range(1,11))

title = [] 
construction = []
price_list = []
effective = []
characteristics = []
agent = []
location = []
link = []

for page in pages:
    url = "https://www.28hse.com/rent/district-64/list-{}".format(page)
    
    #1 locates the url
    data = requests.get(url)
    soup = BeautifulSoup(data.text)
    data = soup.select('div.right.content_me_div')

    #2 get the location
    for i in [i.select('div.catname a[href]') for i in data]:
        if len(i) == 2:
            location.append(i[0].get_text() + ' ' + i[1].get_text())
        else:
            location.append(i[0].get_text())

    #3 get the rent of each properties
    price = soup.find_all('li',{'class':"info_price"})
    for i in price:
        price_list.append(i.text.replace('租','').replace(',','').replace('\xa0',''))

    # get the characteristics of each properties
    p = [i.select('p') for i in data]
    for i in p:
        title.append(i[0].get_text())
        construction.append(i[1].get_text().replace('\xa0','').replace('建築面積 :',''))
        effective.append(i[2].get_text().replace('\xa0','').replace('實用面積:',''))
        characteristics.append(i[3].get_text().replace('\xa0',''))
        agent.append(i[4].get_text().replace('\xa0','').replace('心水樓盤','').strip())
        
    # get the url of each property
    for i in data:
        link.append(i.select('a')[0].get('href'))

# create a dictionary for the dataframe
dict1 = dict(zip(['標題','位置','租金',"建築面積",'實際面積','特點','中介','連結'],
                 [title,location,price_list,construction,effective,characteristics,agent,link]
            )
        )

df = pd.DataFrame(dict1)

# convert the rent to numeric
df['租金'] = pd.to_numeric(df['租金'])

pos = []

for i in df['建築面積']:
    pos.append([i.span()[0] for i in re.finditer(r'呎', i)])

ac = [] 

for i, a in zip(df['建築面積'],pos):
    if len(a):
        ac.append(float(i[0:a[0]]))
    else:
        ac.append(np.nan)
        
df['建築面積'] = ac  

# only include commerical properties and rents are lower than 10000
df_rent = df.loc[(df['特點'].str.contains('住宅') == False) &(df['租金'] < 10000),:]

display(df_rent)
```
Here are the results after running the code
![Result](https://github.com/kwlwman2/Web_Scraping/blob/master/results3.png)
