
### Questions
* Update Many...How do we get this to work?
    * This is something I don't know well...just being honest
* How to take a dataframe and store it as a database
* How to store images in a mongodb

### Objectives
YWBAT
* write data into a mongo db
* read data from a a mongo db

## Steps to run Mongo and PyMongo
* Step 1: Run `mongod` in your terminal
* Step 2: Connect using pymongo Client specifying host and port
* Step 3: Have fun! Or don't...I don't know...

### Outline
* Questions
* Objectives
* 


```python
import json
import requests

import pandas as pd
import numpy as np
from PIL import Image, ImageShow
from io import StringIO, BytesIO

from bs4 import BeautifulSoup

from pymongo import MongoClient

from sklearn.datasets import load_boston

import matplotlib.pyplot as plt
```

| SQL | Mongo |
| ----| ------| 
| Database | Database |
| Table    | Collection |
| Records/Rows | Document |


```python
# Let's create a database and a collection and a document
```


```python
client = MongoClient(host="localhost", port=27017)
```


```python
client.list_database_names() # show dbs in mongo
```




    ['admin', 'config', 'local', 'marchmadness', 'music_tweets', 'mydb', 'tweets']




```python
new_db = client.["new_db"]
```


```python
client.list_database_names()
```




    ['admin',
     'config',
     'local',
     'marchmadness',
     'music_tweets',
     'mydb',
     'new_db',
     'tweets']




```python
students_collection = new_db["students"]
```


```python
keys = ["name", "age", "state", "favorite_color", "favorite_scientist"]

savannah = dict(zip(keys, ['Savannah', 27, 'DC', 'teal', 'stephen hawking']))
savannah
```




    {'name': 'Savannah',
     'age': 27,
     'state': 'DC',
     'favorite_color': 'teal',
     'favorite_scientist': 'stephen hawking'}




```python
# let's insert this dictionary into my new collection
students_collection.insert_one(savannah)
```




    <pymongo.results.InsertOneResult at 0x1a1b7af3c8>




```python
matthew = dict(zip(keys, ['Matthew', 37, 'VA', 'red', 'alan turing']))
students_collection.insert_one(matthew)
```




    <pymongo.results.InsertOneResult at 0x1a1b7af488>




```python
dennis = dict(zip(keys, ['Dennis', 37, 'WV', 'green', 'nikolai tesla']))
students_collection.insert_one(dennis)
```




    <pymongo.results.InsertOneResult at 0x1a1b39d488>




```python
levi = dict(zip(keys, ['Levi', 30, 'NY', 'aqua', 'thomas edison']))
students_collection.insert_one(levi)
```




    <pymongo.results.InsertOneResult at 0x1a1b7a2bc8>




```python
instructors_collection = new_db['instructors']
```


```python
rafael = dict(zip(keys, ['rafael', 32, 'TX', 'purple', 'richard feynmann']))
instructors_collection.insert_one(rafael)
```




    <pymongo.results.InsertOneResult at 0x1a1b39b648>




```python
new_db.list_collection_names()
```




    ['instructors', 'students']



### Filling out forms you can almost guarantee this is going into a database


```python
# select all from students query....how do I do this?
list(students_collection.find({}))
```




    [{'_id': ObjectId('5d02a52d0fd6cac811d94c11'),
      'name': 'Savannah',
      'age': 27,
      'state': 'DC',
      'favorite_color': 'teal',
      'favorite_scientist': 'stephen hawking'},
     {'_id': ObjectId('5d02a5830fd6cac811d94c12'),
      'name': 'Matthew',
      'age': 37,
      'state': 'VA',
      'favorite_color': 'red',
      'favorite_scientist': 'alan turing'},
     {'_id': ObjectId('5d02a5c70fd6cac811d94c13'),
      'name': 'Dennis',
      'age': 37,
      'state': 'WV',
      'favorite_color': 'green',
      'favorite_scientist': 'nikolai tesla'},
     {'_id': ObjectId('5d02a5ff0fd6cac811d94c14'),
      'name': 'Levi',
      'age': 30,
      'state': 'NY',
      'favorite_color': 'aqua',
      'favorite_scientist': 'thomas edison'}]




```python
list(instructors_collection.find({}))
```




    [{'_id': ObjectId('5d02a6400fd6cac811d94c15'),
      'name': 'rafael',
      'age': 32,
      'state': 'TX',
      'favorite_color': 'purple',
      'favorite_scientist': 'richard feynmann'}]




```python
boston_collection = new_db['boston']
```

### Let's make a dataframe and store it as a collection of documents


```python
boston = load_boston()
data = boston.data
columns = boston.feature_names
df = pd.DataFrame(data, columns=columns)
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
      <th>CRIM</th>
      <th>ZN</th>
      <th>INDUS</th>
      <th>CHAS</th>
      <th>NOX</th>
      <th>RM</th>
      <th>AGE</th>
      <th>DIS</th>
      <th>RAD</th>
      <th>TAX</th>
      <th>PTRATIO</th>
      <th>B</th>
      <th>LSTAT</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.00632</td>
      <td>18.0</td>
      <td>2.31</td>
      <td>0.0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>4.0900</td>
      <td>1.0</td>
      <td>296.0</td>
      <td>15.3</td>
      <td>396.90</td>
      <td>4.98</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.02731</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>396.90</td>
      <td>9.14</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.02729</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>392.83</td>
      <td>4.03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.03237</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>6.0622</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>394.63</td>
      <td>2.94</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.06905</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>7.147</td>
      <td>54.2</td>
      <td>6.0622</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>396.90</td>
      <td>5.33</td>
    </tr>
  </tbody>
</table>
</div>



# How can we do this?????......


```python
# iterate through rows
# convert row to dictionary
# insert row
for index, row in df.iterrows():
    doc = dict(row) # NOICE We can turn this row into a dictionary because python is awesome
    boston_collection.insert_one(doc)
```


```python
list(boston_collection.find({}))[:2]
```




    [{'_id': ObjectId('5d02a82d0fd6cac811d94c16'),
      'CRIM': 0.00632,
      'ZN': 18.0,
      'INDUS': 2.31,
      'CHAS': 0.0,
      'NOX': 0.538,
      'RM': 6.575,
      'AGE': 65.2,
      'DIS': 4.09,
      'RAD': 1.0,
      'TAX': 296.0,
      'PTRATIO': 15.3,
      'B': 396.9,
      'LSTAT': 4.98},
     {'_id': ObjectId('5d02a82d0fd6cac811d94c17'),
      'CRIM': 0.02731,
      'ZN': 0.0,
      'INDUS': 7.07,
      'CHAS': 0.0,
      'NOX': 0.469,
      'RM': 6.421,
      'AGE': 78.9,
      'DIS': 4.9671,
      'RAD': 2.0,
      'TAX': 242.0,
      'PTRATIO': 17.8,
      'B': 396.9,
      'LSTAT': 9.14}]




```python
boston2_coll = new_db["boston2"]
```


```python
# or insert all at once using to_json and json_loads
boston2_coll.insert_many(json.loads(df.to_json(orient='records')))
```




    <pymongo.results.InsertManyResult at 0x1a1a423108>




```python
res1 = list(boston_collection.find({}))
res2 = list(boston2_coll.find({}))
```


```python
res1[0]
```




    {'_id': ObjectId('5d02a82d0fd6cac811d94c16'),
     'CRIM': 0.00632,
     'ZN': 18.0,
     'INDUS': 2.31,
     'CHAS': 0.0,
     'NOX': 0.538,
     'RM': 6.575,
     'AGE': 65.2,
     'DIS': 4.09,
     'RAD': 1.0,
     'TAX': 296.0,
     'PTRATIO': 15.3,
     'B': 396.9,
     'LSTAT': 4.98}




```python
res2[0]
```




    {'_id': ObjectId('5d02a9690fd6cac811d94e10'),
     'CRIM': 0.00632,
     'ZN': 18.0,
     'INDUS': 2.31,
     'CHAS': 0.0,
     'NOX': 0.538,
     'RM': 6.575,
     'AGE': 65.2,
     'DIS': 4.09,
     'RAD': 1.0,
     'TAX': 296.0,
     'PTRATIO': 15.3,
     'B': 396.9,
     'LSTAT': 4.98}



# Let's scrape some ebay images and store in a Mongo DB

# Step 1 - Get the images


```python
url = "https://www.ebay.com/sch/i.html?_nkw=funko+pop"
```


```python
req = requests.get(url)
```


```python
soup = BeautifulSoup(req.content, 'html.parser')
```


```python
image_urls = [tag.get('src') for tag in soup.find_all("img", class_="s-item__image-img") if tag.get('src').startswith('https://i.ebayimg.com/')]
image_urls
```




    ['https://i.ebayimg.com/thumbs/images/g/JlsAAOSwAYlcsNpc/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/RaoAAOSwPYdcbteC/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/QHUAAOSwQXlcUNZI/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/YrMAAOSwZXpc-UBQ/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/WAAAAOSwiSJbmWmJ/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/4wQAAOSw6GhcdJph/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/BW4AAOSwootcv5BX/s-l225.jpg']




```python
r = requests.get(image_urls[0])
r.content
i = Image.open(BytesIO(r.content))
```


```python
funko_coll = new_db['funko']
```


```python
funko_coll.insert_one({'image_content': r.content})
```




    <pymongo.results.InsertOneResult at 0x1a218a4b08>




```python
for url in image_urls[1:]:
    r = requests.get(url)
    r.content
    i = Image.open(BytesIO(r.content))
    funko_coll.insert_one({'image_content': r.content})
```


```python
Image.open(BytesIO(list(funko_coll.find({}, {'image_content'}))[0]['image_content']))
```




![png](lesson-plan_files/lesson-plan_44_0.png)




```python
for res in funko_coll.find({}):
    img = Image.open(BytesIO(res['image_content']))
    img.show()
```


```python

```


```python

```

### What did we learn today?
* How to make a table in a markdown cell
* Convert a Dataframe into a MongoDB
* How to scrape images and store them in a mongodb
* insert_many
* Learned more about each other #community


```python

```
