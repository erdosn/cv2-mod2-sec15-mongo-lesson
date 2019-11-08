
### Questions

### Objectives
YWBAT
* write data into a mongo db
* read data from a mongo db

## Steps to run Mongo and PyMongo
* Step 1: Run `mongod` in your terminal
* Step 2: Connect using pymongo Client specifying host and port
* Step 3: Have fun! Or don't...I don't know...

### Outline
* Objectives
* Questions
* Discuss the differences between MongoDB and SQL (Relational DBs)
* Scrape webpage data and store it in a MongoDB that we create


```python
import json
import requests

import pandas as pd
import numpy as np

from pymongo import MongoClient

import matplotlib.pyplot as plt
```

| SQL | Mongo |
| ----| ------| 
| Database | Database |
| Table    | Collection |
| Records/Rows | Document |

# Let's Start Up my Local Mongo Server

run `mongod` in terminal

# Connect to Local Mongo Server with Python


```python
# What is a host? server ip
# What is a port? connect through a port, so it's the location to plug in our connection
client = MongoClient('localhost', 27017)
```


```python
# let's make a new database called ebay
ebay = client['ebay']
```


```python
client.list_database_names()
```




    ['admin', 'config', 'local', 'music_tweets', 'new_db', 'tweets']




```python
# let's make a collection for our database
tech_bags = ebay['tech_bags']
```


```python
client.list_database_names()
```




    ['admin', 'config', 'local', 'music_tweets', 'new_db', 'tweets']



Notice our database hasn't been added. This is because we have to add a document to a collection in that database.

### let me make a fake table that I won't use with a document, just to make the database


```python
collection_one = ebay['collection_one']
```


```python
# let's create a document to put into our MongoDB
document = {"name": "Moana", "age": 15}
```


```python
collection_one.insert_one(document)
```




    <pymongo.results.InsertOneResult at 0x123f93b08>




```python
client.list_database_names()
```




    ['admin', 'config', 'ebay', 'local', 'music_tweets', 'new_db', 'tweets']



### when you write a document to a collection, the database gets created

# let's scrape some ebay data and store it to our mongoDB


```python
import requests
from bs4 import BeautifulSoup
```


```python
url = "https://www.ebay.com/sch/i.html?_nkw=tech+backpack"
```


```python
req = requests.get(url)
```


```python
# beautiful soup - library used for parsing an html/xml document
soup = BeautifulSoup(req.content, 'html.parser')
```


```python
# let's use the item-wrapper to get the boxes of data
# this creates a list of soup objects
boxes = soup.find_all("div", class_="s-item__wrapper clearfix")
```


```python
for box in boxes:
    box_dict = {}

    box_dict["name"] = box.find("h3", class_="s-item__title").text
    box_dict["price"] = box.find("span", class_="s-item__price").text
    box_dict["shipping_info"] = box.find("span", class_="s-item__shipping").text
    try:
        review_box = box.find("div", class_="s-item__reviews").find_all("svg")
        box_dict["num_stars"] = len(review_box)
    except:
        review_box = None
        box_dict["num_stars"] = None
```

# now let's do this process, but over all the pages



```python
url = "https://www.ebay.com/sch/i.html?_nkw=tech+backpack&_pgn={}"
```


```python
urls = [url.format(i) for i in range(1, 20)]
urls[:5]
```




    ['https://www.ebay.com/sch/i.html?_nkw=tech+backpack&_pgn=1',
     'https://www.ebay.com/sch/i.html?_nkw=tech+backpack&_pgn=2',
     'https://www.ebay.com/sch/i.html?_nkw=tech+backpack&_pgn=3',
     'https://www.ebay.com/sch/i.html?_nkw=tech+backpack&_pgn=4',
     'https://www.ebay.com/sch/i.html?_nkw=tech+backpack&_pgn=5']



# Now that my URLs are made, I'm going to loop through all of them and scrape


```python
for index, url in enumerate(urls):
    print("-"*50)
    print("Scraping Page {}".format(index + 1))
    req = requests.get(url)
    soup = BeautifulSoup(req.content, 'html.parser')
    boxes = soup.find_all("div", class_="s-item__wrapper clearfix")
    for box in boxes:
        box_dict = {}

        try:
            box_dict["name"] = box.find("h3", class_="s-item__title").text
        except:
            box_dict["name"] = None
            
        try:
            box_dict["price"] = box.find("span", class_="s-item__price").text
        except:
            box_dict["price"] = None
        
        try:
            box_dict["shipping_info"] = box.find("span", class_="s-item__shipping").text
        except:
            box_dict["shipping_info"] = None
        
        try:
            review_box = box.find("div", class_="s-item__reviews").find_all("svg")
            box_dict["num_stars"] = len(review_box)
        except:
            review_box = None
            box_dict["num_stars"] = None
        
        # Now we insert this dictionary into our db
        
        tech_bags.insert_one(box_dict)
        total_docs = tech_bags.count_documents(filter={})
        print("inserted document, total_docs = {}".format(total_docs))
```

### Let's query on our collection


```python
# let's filter free shipping items only
filt = {"shipping_info" : {"$regex" : "Free"}}
```


```python
for i, r in enumerate(tech_bags.find(filt, limit=5)):
    print(r)
    print("\n\n")
```

    {'_id': ObjectId('5dc5aaeef84ae6c2f447fafd'), 'name': 'Tech Backpack BackPack - Smart Laptop Backpack USB Charging with Free Shipping!', 'price': '$21.12', 'shipping_info': 'Free Shipping', 'num_stars': None}
    
    
    
    {'_id': ObjectId('5dc5aaeef84ae6c2f447fafe'), 'name': "Michael Kors Men's Kent Nylon Army Camo Tech Backpack (Select Color)", 'price': '$100.65', 'shipping_info': 'Free Shipping', 'num_stars': None}
    
    
    
    {'_id': ObjectId('5dc5aaeef84ae6c2f447faff'), 'name': 'MICHAEL KORS MENS KENT TECH NYLON ARMY CAMO BACKPACK BOOKBAG BAG INDIGO BLUE ', 'price': '$69.99', 'shipping_info': 'Free Shipping', 'num_stars': 5}
    
    
    
    {'_id': ObjectId('5dc5aaeef84ae6c2f447fb01'), 'name': 'MICHAEL KORS MENS KENT TECH NYLON CAMO LOGO STRIPE BACKPACK BOOKBAG BAG BLACK', 'price': '$69.99', 'shipping_info': 'Free Shipping', 'num_stars': None}
    
    
    
    {'_id': ObjectId('5dc5aaeef84ae6c2f447fb03'), 'name': 'MICHAEL KORS MENS KENT TECH NYLON ARMY BLACK CAMO BACKPACK BOOKBAG BAG NEW', 'price': '$75.00', 'shipping_info': 'Free Shipping', 'num_stars': 5}
    
    
    


# Let's load this into pandas


```python
dict1 = {"name": "Rafael", "age": 33}
dict2 = {"name": "Doug", "city": "Dallas", "population": "???"}


df = pd.DataFrame([dict1, dict2])
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>age</th>
      <th>city</th>
      <th>population</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Rafael</td>
      <td>33.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Doug</td>
      <td>NaN</td>
      <td>Dallas</td>
      <td>???</td>
    </tr>
  </tbody>
</table>
</div>



# let's load our mongodb filter in a dataframe



```python
list_of_docs = list(tech_bags.find(filt))

df = pd.DataFrame(list_of_docs)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>_id</th>
      <th>name</th>
      <th>price</th>
      <th>shipping_info</th>
      <th>num_stars</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>5dc5aaeef84ae6c2f447fafd</td>
      <td>Tech Backpack BackPack - Smart Laptop Backpack...</td>
      <td>$21.12</td>
      <td>Free Shipping</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>1</td>
      <td>5dc5aaeef84ae6c2f447fafe</td>
      <td>Michael Kors Men's Kent Nylon Army Camo Tech B...</td>
      <td>$100.65</td>
      <td>Free Shipping</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>2</td>
      <td>5dc5aaeef84ae6c2f447faff</td>
      <td>MICHAEL KORS MENS KENT TECH NYLON ARMY CAMO BA...</td>
      <td>$69.99</td>
      <td>Free Shipping</td>
      <td>5.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>5dc5aaeef84ae6c2f447fb01</td>
      <td>MICHAEL KORS MENS KENT TECH NYLON CAMO LOGO ST...</td>
      <td>$69.99</td>
      <td>Free Shipping</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5dc5aaeef84ae6c2f447fb03</td>
      <td>MICHAEL KORS MENS KENT TECH NYLON ARMY BLACK C...</td>
      <td>$75.00</td>
      <td>Free Shipping</td>
      <td>5.0</td>
    </tr>
  </tbody>
</table>
</div>



### What did we learn today?
- Use try/except to handle missing info
- How to load a mongo filter into a dataframe
- How to set up a mongodb
- Learned that Beautiful Soup is a parser
- Reading/Writing data from/into a MongoDB


```python

```
