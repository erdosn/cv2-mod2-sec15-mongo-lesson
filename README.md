
### Questions
- Column Stores vs Key: Value 
- RDDs

### Objectives
YWBAT
* write data into a mongo db
* read data from a mongo db

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
import tweepy

import pandas as pd
import numpy as np
from PIL import Image, ImageShow
from io import StringIO, BytesIO

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




    ['admin', 'config', 'local', 'music_tweets', 'tweets']




```python
new_db = client["new_db"]
```


```python
students_collection = new_db["students"]
```


```python
client.list_database_names()
```




    ['admin', 'config', 'local', 'music_tweets', 'tweets']




```python
instructors_collection = new_db['instructors']
```


```python
new_db.list_collection_names()
```




    []



### Let's populate students



```python
def fill_form():
    form = {}
    
    name = input("Enter Name: ")
    form['name'] = name
    
    age = int(input("Enter Age: "))
    form['age'] = age
    
    favorite_instructor = "Rafael"
    form["favorite_instructor"] = favorite_instructor
    
    favorite_cartoon = input("What's your favorite cartoon? ")
    form["favorite_cartoon"] = favorite_cartoon
    
    state = input("State of Residence")
    form["state"] = state
    
    track = input("What are you studying?\n1. SE\n2. DS\n3.UI/UX")
    form["track"] = track
    return form
```


```python
def form_input_flow(client, db='new_db', collection='students'):
    form = fill_form()
    
    # created a variable for my collection
    coll = client[db][collection]
    coll.insert_one(form)
    total_docs = coll.count_documents()
    print(f"Inserted a new form, you now have {total_docs} documents in {collection}")
    return None
```


```python
while True:
    form_input_flow(client=client)
```

    Enter Name: Andrew Smith
    Enter Age: 31
    What's your favorite cartoon? DBZ
    State of ResidenceTX
    What are you studying?
    1. SE
    2. DS
    3.UI/UXDS


    /Users/rafael/anaconda3/envs/flatiron-env/lib/python3.6/site-packages/ipykernel_launcher.py:7: DeprecationWarning: count is deprecated. Use estimated_document_count or count_documents instead. Please note that $where must be replaced by $expr, $near must be replaced by $geoWithin with $center, and $nearSphere must be replaced by $geoWithin with $centerSphere
      import sys


    Inserted a new form, you now have 1 documents in students
    Enter Name: Bryan Jamieson
    Enter Age: 24
    What's your favorite cartoon? Scoobie-Doo
    State of ResidenceOH
    What are you studying?
    1. SE
    2. DS
    3.UI/UXDS
    Inserted a new form, you now have 2 documents in students
    Enter Name: Jeffrey Hinkle
    Enter Age: 44
    What's your favorite cartoon? Tom and Jerry
    State of ResidenceGA
    What are you studying?
    1. SE
    2. DS
    3.UI/UXDS
    Inserted a new form, you now have 3 documents in students
    Enter Name: Dr. Who
    Enter Age: 25
    What's your favorite cartoon? DBS
    State of ResidenceGA
    What are you studying?
    1. SE
    2. DS
    3.UI/UXDS
    Inserted a new form, you now have 4 documents in students
    Enter Name: Mathew Menarchy
    Enter Age: 35
    What's your favorite cartoon? Animaniacs
    State of ResidenceTN
    What are you studying?
    1. SE
    2. DS
    3.UI/UXDS
    Inserted a new form, you now have 5 documents in students



    -----------------------------------------------

    KeyboardInterruptTraceback (most recent call last)

    ~/anaconda3/envs/flatiron-env/lib/python3.6/site-packages/ipykernel/kernelbase.py in _input_request(self, prompt, ident, parent, password)
        884             try:
    --> 885                 ident, reply = self.session.recv(self.stdin_socket, 0)
        886             except Exception:


    ~/anaconda3/envs/flatiron-env/lib/python3.6/site-packages/jupyter_client/session.py in recv(self, socket, mode, content, copy)
        802         try:
    --> 803             msg_list = socket.recv_multipart(mode, copy=copy)
        804         except zmq.ZMQError as e:


    ~/anaconda3/envs/flatiron-env/lib/python3.6/site-packages/zmq/sugar/socket.py in recv_multipart(self, flags, copy, track)
        474         """
    --> 475         parts = [self.recv(flags, copy=copy, track=track)]
        476         # have first part already, only loop while more to receive


    zmq/backend/cython/socket.pyx in zmq.backend.cython.socket.Socket.recv()


    zmq/backend/cython/socket.pyx in zmq.backend.cython.socket.Socket.recv()


    zmq/backend/cython/socket.pyx in zmq.backend.cython.socket._recv_copy()


    ~/anaconda3/envs/flatiron-env/lib/python3.6/site-packages/zmq/backend/cython/checkrc.pxd in zmq.backend.cython.checkrc._check_rc()


    KeyboardInterrupt: 

    
    During handling of the above exception, another exception occurred:


    KeyboardInterruptTraceback (most recent call last)

    <ipython-input-24-71d0a0d52764> in <module>
          1 while True:
    ----> 2     form_input_flow(client=client)
    

    <ipython-input-23-2348984e1a55> in form_input_flow(client, db, collection)
          1 def form_input_flow(client, db='new_db', collection='students'):
    ----> 2     form = fill_form()
          3 
          4     # created a variable for my collection
          5     coll = client[db][collection]


    <ipython-input-21-bca7e9067b23> in fill_form()
          2     form = {}
          3 
    ----> 4     name = input("Enter Name: ")
          5     form['name'] = name
          6 


    ~/anaconda3/envs/flatiron-env/lib/python3.6/site-packages/ipykernel/kernelbase.py in raw_input(self, prompt)
        858             self._parent_ident,
        859             self._parent_header,
    --> 860             password=False,
        861         )
        862 


    ~/anaconda3/envs/flatiron-env/lib/python3.6/site-packages/ipykernel/kernelbase.py in _input_request(self, prompt, ident, parent, password)
        888             except KeyboardInterrupt:
        889                 # re-raise KeyboardInterrupt, to truncate traceback
    --> 890                 raise KeyboardInterrupt
        891             else:
        892                 break


    KeyboardInterrupt: 


### Filling out forms you can almost guarantee this is going into a database


```python
students_collection.count_documents(filter={})
```




    5




```python
list(students_collection.find({"age": {"$gt": 30}}))
```




    [{'_id': ObjectId('5d9f7a4e060ef7ed5960f9d9'),
      'name': 'Andrew Smith',
      'age': 31,
      'favorite_instructor': 'Rafael',
      'favorite_cartoon': 'DBZ',
      'state': 'TX',
      'track': 'DS'},
     {'_id': ObjectId('5d9f7ac1060ef7ed5960f9db'),
      'name': 'Jeffrey Hinkle',
      'age': 44,
      'favorite_instructor': 'Rafael',
      'favorite_cartoon': 'Tom and Jerry',
      'state': 'GA',
      'track': 'DS'},
     {'_id': ObjectId('5d9f7b1e060ef7ed5960f9dd'),
      'name': 'Mathew Menarchy',
      'age': 35,
      'favorite_instructor': 'Rafael',
      'favorite_cartoon': 'Animaniacs',
      'state': 'TN',
      'track': 'DS'}]




```python
new_student = {"name": "The Master"}
students_collection.insert_one(new_student)

list(students_collection.find({}))
```




    [{'_id': ObjectId('5d9f7a4e060ef7ed5960f9d9'),
      'name': 'Andrew Smith',
      'age': 31,
      'favorite_instructor': 'Rafael',
      'favorite_cartoon': 'DBZ',
      'state': 'TX',
      'track': 'DS'},
     {'_id': ObjectId('5d9f7a92060ef7ed5960f9da'),
      'name': 'Bryan Jamieson',
      'age': 24,
      'favorite_instructor': 'Rafael',
      'favorite_cartoon': 'Scoobie-Doo',
      'state': 'OH',
      'track': 'DS'},
     {'_id': ObjectId('5d9f7ac1060ef7ed5960f9db'),
      'name': 'Jeffrey Hinkle',
      'age': 44,
      'favorite_instructor': 'Rafael',
      'favorite_cartoon': 'Tom and Jerry',
      'state': 'GA',
      'track': 'DS'},
     {'_id': ObjectId('5d9f7af3060ef7ed5960f9dc'),
      'name': 'Dr. Who',
      'age': 25,
      'favorite_instructor': 'Rafael',
      'favorite_cartoon': 'DBS',
      'state': 'GA',
      'track': 'DS'},
     {'_id': ObjectId('5d9f7b1e060ef7ed5960f9dd'),
      'name': 'Mathew Menarchy',
      'age': 35,
      'favorite_instructor': 'Rafael',
      'favorite_cartoon': 'Animaniacs',
      'state': 'TN',
      'track': 'DS'},
     {'_id': ObjectId('5d9f7c45060ef7ed5960f9de'), 'name': 'The Master'}]




```python
df = pd.DataFrame(list(students_collection.find({})))
df.head(10)
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
      <th>age</th>
      <th>favorite_instructor</th>
      <th>favorite_cartoon</th>
      <th>state</th>
      <th>track</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>5d9f7a4e060ef7ed5960f9d9</td>
      <td>Andrew Smith</td>
      <td>31.0</td>
      <td>Rafael</td>
      <td>DBZ</td>
      <td>TX</td>
      <td>DS</td>
    </tr>
    <tr>
      <td>1</td>
      <td>5d9f7a92060ef7ed5960f9da</td>
      <td>Bryan Jamieson</td>
      <td>24.0</td>
      <td>Rafael</td>
      <td>Scoobie-Doo</td>
      <td>OH</td>
      <td>DS</td>
    </tr>
    <tr>
      <td>2</td>
      <td>5d9f7ac1060ef7ed5960f9db</td>
      <td>Jeffrey Hinkle</td>
      <td>44.0</td>
      <td>Rafael</td>
      <td>Tom and Jerry</td>
      <td>GA</td>
      <td>DS</td>
    </tr>
    <tr>
      <td>3</td>
      <td>5d9f7af3060ef7ed5960f9dc</td>
      <td>Dr. Who</td>
      <td>25.0</td>
      <td>Rafael</td>
      <td>DBS</td>
      <td>GA</td>
      <td>DS</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5d9f7b1e060ef7ed5960f9dd</td>
      <td>Mathew Menarchy</td>
      <td>35.0</td>
      <td>Rafael</td>
      <td>Animaniacs</td>
      <td>TN</td>
      <td>DS</td>
    </tr>
    <tr>
      <td>5</td>
      <td>5d9f7c45060ef7ed5960f9de</td>
      <td>The Master</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



### Let's put a dataframe into our mongodb


```python
from sklearn.datasets import load_boston
boston = load_boston()
data = boston.data
target = boston.target
features = boston.feature_names

df = pd.DataFrame(data, columns=features)
df['target'] = target
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
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
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
      <td>24.0</td>
    </tr>
    <tr>
      <td>1</td>
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
      <td>21.6</td>
    </tr>
    <tr>
      <td>2</td>
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
      <td>34.7</td>
    </tr>
    <tr>
      <td>3</td>
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
      <td>33.4</td>
    </tr>
    <tr>
      <td>4</td>
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
      <td>36.2</td>
    </tr>
  </tbody>
</table>
</div>




```python
boston_collection = new_db['boston']

for index, row in df.iterrows():
    # now insert every row into our collection
    boston_collection.insert_one(dict(row))

df.shape, boston_collection.count_documents({})
```




    ((506, 14), 506)




```python
rows = [dict(row) for i, row in df.iterrows()]

boston_collection.insert_many(rows)

boston_collection.count_documents({})
```




    1012




```python

```


```python

```

### Let's make a dataframe and store it as a collection of documents

# How can we do this?????......

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




    ['https://i.ebayimg.com/thumbs/images/g/sfsAAOSwB21c6~bF/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/thYAAOSwARpdArg1/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/RaoAAOSwPYdcbteC/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/JlsAAOSwAYlcsNpc/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/YrMAAOSwZXpc-UBQ/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/4wQAAOSw6GhcdJph/s-l225.jpg',
     'https://i.ebayimg.com/thumbs/images/g/0voAAOSwgRpc8EgZ/s-l225.jpg']




```python
r = requests.get(image_urls[0])
r.content
i = Image.open(BytesIO(r.content))
i
```




![png](lesson-plan_files/lesson-plan_37_0.png)




```python
new_db = client['new_db']
```


```python
funko_coll = new_db['funko']
```


```python
funko_coll.insert_one({'image_content': r.content})
```




    <pymongo.results.InsertOneResult at 0x1a218a4b08>




```python
for url in image_urls:
    r = requests.get(url)
    r.content
    i = Image.open(BytesIO(r.content))
    funko_coll.insert_one({'image_content': r.content})
```


```python
Image.open(BytesIO(list(funko_coll.find({}, {'image_content'}))[-1]['image_content']))
```




![png](lesson-plan_files/lesson-plan_42_0.png)




```python
for res in funko_coll.find({}):
    img = Image.open(BytesIO(res['image_content']))
    img.show()
```


```python
tweets = client['tweets']
```


```python
donald_tweets = tweets["donald_tweets"]
```


```python
from pprint import pprint
for tweet in donald_tweets.find({}):
    pprint(tweet)
    break
```

    {'_id': ObjectId('5c8f9c97191c8235fd5613a7'),
     'contributors': None,
     'coordinates': None,
     'created_at': 'Mon Mar 18 13:26:42 +0000 2019',
     'entities': {'hashtags': [],
                  'symbols': [],
                  'urls': [{'display_url': 'twitter.com/i/web/status/1…',
                            'expanded_url': 'https://twitter.com/i/web/status/1107634434432532481',
                            'indices': [117, 140],
                            'url': 'https://t.co/twRWeJ9rFG'}],
                  'user_mentions': []},
     'extended_tweet': {'display_text_range': [0, 165],
                        'entities': {'hashtags': [],
                                     'symbols': [],
                                     'urls': [],
                                     'user_mentions': []},
                        'full_text': 'I will have to check my UNFOLLOW group that '
                                     "allows me to see tRUMP's tweets without "
                                     'giving him credit for me or anyone else '
                                     'following him if they so choose. I chose!'},
     'favorite_count': 0,
     'favorited': False,
     'filter_level': 'low',
     'geo': None,
     'id': 1107634434432532481,
     'id_str': '1107634434432532481',
     'in_reply_to_screen_name': None,
     'in_reply_to_status_id': None,
     'in_reply_to_status_id_str': None,
     'in_reply_to_user_id': None,
     'in_reply_to_user_id_str': None,
     'is_quote_status': True,
     'lang': 'en',
     'place': None,
     'quote_count': 0,
     'quoted_status': {'contributors': None,
                       'coordinates': None,
                       'created_at': 'Sun Mar 17 18:34:36 +0000 2019',
                       'entities': {'hashtags': [],
                                    'symbols': [],
                                    'urls': [{'display_url': 'twitter.com/i/web/status/1…',
                                              'expanded_url': 'https://twitter.com/i/web/status/1107349530260000774',
                                              'indices': [116, 139],
                                              'url': 'https://t.co/rGbWdotUtl'}],
                                    'user_mentions': []},
                       'extended_tweet': {'display_text_range': [0, 280],
                                          'entities': {'hashtags': [],
                                                       'symbols': [],
                                                       'urls': [],
                                                       'user_mentions': [{'id': 12,
                                                                          'id_str': '12',
                                                                          'indices': [143,
                                                                                      148],
                                                                          'name': 'jack',
                                                                          'screen_name': 'jack'}]},
                                          'full_text': 'I’ve reported all of '
                                                       'tRumps tweets on twitter '
                                                       'today. He’s becoming more '
                                                       'aggressive and hostile. '
                                                       'What has to happen to get '
                                                       'tRump out of the WH. @jack '
                                                       '— why does Twitter '
                                                       'continue to allow someone '
                                                       'that’s clearly not '
                                                       'mentally stable to use '
                                                       'Twitter? tRumps account '
                                                       'should be suspended.'},
                       'favorite_count': 774,
                       'favorited': False,
                       'filter_level': 'low',
                       'geo': None,
                       'id': 1107349530260000774,
                       'id_str': '1107349530260000774',
                       'in_reply_to_screen_name': None,
                       'in_reply_to_status_id': None,
                       'in_reply_to_status_id_str': None,
                       'in_reply_to_user_id': None,
                       'in_reply_to_user_id_str': None,
                       'is_quote_status': False,
                       'lang': 'en',
                       'place': None,
                       'quote_count': 14,
                       'reply_count': 63,
                       'retweet_count': 262,
                       'retweeted': False,
                       'source': '<a href="http://twitter.com/download/iphone" '
                                 'rel="nofollow">Twitter for iPhone</a>',
                       'text': 'I’ve reported all of tRumps tweets on twitter '
                               'today. He’s becoming more aggressive and hostile. '
                               'What has to happen… https://t.co/rGbWdotUtl',
                       'truncated': True,
                       'user': {'contributors_enabled': False,
                                'created_at': 'Thu Jul 07 12:33:13 +0000 2016',
                                'default_profile': False,
                                'default_profile_image': False,
                                'description': 'Renegade Ex Republican. Now a '
                                               '(D).There can be no real peace '
                                               'without Justice. And without '
                                               '#Resistance there will be no '
                                               'Justice. #AlwaysWithHer '
                                               '#AlwaysWithStacey',
                                'favourites_count': 38995,
                                'follow_request_sent': None,
                                'followers_count': 33631,
                                'following': None,
                                'friends_count': 25424,
                                'geo_enabled': False,
                                'id': 751031312648171520,
                                'id_str': '751031312648171520',
                                'is_translator': False,
                                'lang': 'en',
                                'listed_count': 69,
                                'location': 'Atlanta, Ga',
                                'name': 'Debbie',
                                'notifications': None,
                                'profile_background_color': '000000',
                                'profile_background_image_url': 'http://abs.twimg.com/images/themes/theme1/bg.png',
                                'profile_background_image_url_https': 'https://abs.twimg.com/images/themes/theme1/bg.png',
                                'profile_background_tile': False,
                                'profile_banner_url': 'https://pbs.twimg.com/profile_banners/751031312648171520/1480811561',
                                'profile_image_url': 'http://pbs.twimg.com/profile_images/1106681706331942912/uq3WKYpw_normal.jpg',
                                'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1106681706331942912/uq3WKYpw_normal.jpg',
                                'profile_link_color': 'F58EA8',
                                'profile_sidebar_border_color': '000000',
                                'profile_sidebar_fill_color': '000000',
                                'profile_text_color': '000000',
                                'profile_use_background_image': False,
                                'protected': False,
                                'screen_name': 'Dangchick1',
                                'statuses_count': 62249,
                                'time_zone': None,
                                'translator_type': 'none',
                                'url': None,
                                'utc_offset': None,
                                'verified': False}},
     'quoted_status_id': 1107349530260000774,
     'quoted_status_id_str': '1107349530260000774',
     'quoted_status_permalink': {'display': 'twitter.com/Dangchick1/sta…',
                                 'expanded': 'https://twitter.com/Dangchick1/status/1107349530260000774',
                                 'url': 'https://t.co/9NvK3sVAuB'},
     'reply_count': 0,
     'retweet_count': 0,
     'retweeted': False,
     'source': '<a href="https://mobile.twitter.com" rel="nofollow">Twitter Web '
               'App</a>',
     'text': "I will have to check my UNFOLLOW group that allows me to see tRUMP's "
             'tweets without giving him credit for me or any… '
             'https://t.co/twRWeJ9rFG',
     'timestamp_ms': '1552915602678',
     'truncated': True,
     'user': {'contributors_enabled': False,
              'created_at': 'Sat Mar 28 00:21:12 +0000 2009',
              'default_profile': False,
              'default_profile_image': False,
              'description': '"Itinerant" Army Brat, HAPPILY married mother of 2 '
                             'grown children, RN Retired, NOT looking for any '
                             'boyfriends, no DMs, lists or any of that other '
                             'stuff.',
              'favourites_count': 38077,
              'follow_request_sent': None,
              'followers_count': 1115,
              'following': None,
              'friends_count': 2798,
              'geo_enabled': True,
              'id': 27136763,
              'id_str': '27136763',
              'is_translator': False,
              'lang': 'en',
              'listed_count': 0,
              'location': 'Mississippi, USA',
              'name': 'Linda Meylan',
              'notifications': None,
              'profile_background_color': '2CB85F',
              'profile_background_image_url': 'http://abs.twimg.com/images/themes/theme13/bg.gif',
              'profile_background_image_url_https': 'https://abs.twimg.com/images/themes/theme13/bg.gif',
              'profile_background_tile': True,
              'profile_banner_url': 'https://pbs.twimg.com/profile_banners/27136763/1482337602',
              'profile_image_url': 'http://pbs.twimg.com/profile_images/1073365044631650305/yDtrbIr9_normal.jpg',
              'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1073365044631650305/yDtrbIr9_normal.jpg',
              'profile_link_color': '1B95E0',
              'profile_sidebar_border_color': 'CC588C',
              'profile_sidebar_fill_color': 'E3D16D',
              'profile_text_color': '0A2270',
              'profile_use_background_image': False,
              'protected': False,
              'screen_name': 'Lmeylan',
              'statuses_count': 11883,
              'time_zone': None,
              'translator_type': 'none',
              'url': None,
              'utc_offset': None,
              'verified': False}}


### What did we learn today?
* the syntax for mongo is a dictionary
* you can insert dataframes into mongo using iterrows and casting each row as a dictionary
* you can insert anything you want into a mongo collection, even if it's different from the collection
* pandas will impose structure on a dictionary list


```python

```
