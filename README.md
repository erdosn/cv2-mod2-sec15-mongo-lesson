
### Questions
* run mongo from start to jupyter to finish? 
* keyvalue vs doc stores?

### Objectives
YWBAT
* write data into a mongo db
* read data from a mongo db
* write a data processing job using pymongo



## Steps to run Mongo and PyMongo
* Step 1: Run `mongod` in your terminal
    * to interact with it you can use terminal or python
* Step 2: Connect using pymongo Client specifying host and port
* Step 3: Have fun! Or don't...I don't know...

### Outline
* Questions
* Objectives
* 


```python
import json 
from pymongo import MongoClient
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
client = MongoClient(host='localhost', port=27017)
```


```python
# how do we make stuff in our mongodb?
```


```python
client.list_database_names()
```




    ['admin',
     'config',
     'ebay',
     'headstorm_client_db',
     'local',
     'music_tweets',
     'new_db',
     'tweets']




```python
ebay = client['ebay']
```


```python
type(ebay)
```




    pymongo.database.Database




```python
ebay.list_collection_names()
```




    ['collection_one', 'tech_bags']




```python
tech_bags_collection = ebay['tech_bags']
tech_bags_collection
```




    Collection(Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), 'ebay'), 'tech_bags')




```python
client['ebay']['tech_bags']
```




    Collection(Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), 'ebay'), 'tech_bags')




```python
aprft = client['AprFT']
```


```python
client.list_database_names()
```




    ['admin',
     'config',
     'ebay',
     'headstorm_client_db',
     'local',
     'music_tweets',
     'new_db',
     'tweets']




```python
students = aprft['students']
```


```python
type(students)
```




    pymongo.collection.Collection




```python
aprft.list_collection_names()
```




    []




```python
# schema {"name", "state"}
student1 = {"name":"Heather", "state": "Texas"}
student2 = {"name":"Thomas", "state":"California"}
student3 = {"name":"Erica", "state":"California"}
student4 = {"name":"Megan", "county": "Greater London"}
```

# How do we insert data? 


```python
students.insert_one(student4)
```




    <pymongo.results.InsertOneResult at 0x10f35a788>



# now let's check our dbs


```python
client.list_database_names()
```




    ['AprFT',
     'admin',
     'config',
     'ebay',
     'headstorm_client_db',
     'local',
     'music_tweets',
     'new_db',
     'tweets']




```python
client['AprFT'].list_collection_names()
```




    ['students']




```python
student_list = [{"name": "leighanna", "state": "Michigan"},
                {"name": "payson", "state": "Ohio"},
                {"name": "edward", "province": "British Columbia"}]
```


```python
students.insert_many(student_list)
```




    <pymongo.results.InsertManyResult at 0x10f37ba08>



# After inserting some documents, let's look at them


```python
# count the number of docs?
import warnings
warnings.filterwarnings('ignore')
students.count()
```




    7




```python
# let's do a select * from students
for res in students.find(filter={}):
    print(res)
```

    {'_id': ObjectId('5ea9de25898be8b2d4711c89'), 'name': 'Jonny', 'county': None}
    {'_id': ObjectId('5ea9ded3898be8b2d4711c8a'), 'name': 'Heather', 'state': 'Texas'}
    {'_id': ObjectId('5ea9ded5898be8b2d4711c8b'), 'name': 'Thomas', 'state': 'California'}
    {'_id': ObjectId('5ea9ded7898be8b2d4711c8c'), 'name': 'Erica', 'state': 'California'}
    {'_id': ObjectId('5ea9ded9898be8b2d4711c8d'), 'name': 'Megan', 'county': 'Greater London'}
    {'_id': ObjectId('5ea9dedb898be8b2d4711c8e'), 'name': 'leighanna', 'state': 'Michigan'}
    {'_id': ObjectId('5ea9dedb898be8b2d4711c8f'), 'name': 'payson', 'state': 'Ohio'}
    {'_id': ObjectId('5ea9dedb898be8b2d4711c90'), 'name': 'edward', 'province': 'British Columbia'}



```python
# how do i find edward?

for res in students.find(filter={"name":"edward"}):
    print(res)
```

    {'_id': ObjectId('5ea9d772898be8b2d4711c87'), 'name': 'edward', 'province': 'British Columbia'}



```python
# how do i find all students whose or who's name begins with an 'e'?
# SQL -> select * from students where name like 'e%';
# Mongo -> {"name": "$regex": {"^e", "$options": "i"}}


for res in students.find(filter={"name": {"$regex": "^e", "$options": "i"}}):
    print(res)
```

    {'_id': ObjectId('5ea9d655898be8b2d4711c83'), 'name': 'Erica', 'state': 'California'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c87'), 'name': 'edward', 'province': 'British Columbia'}


Why is this so complicated? 
* Because Mongo can contain any key, value pairs within 1 collection. 


```python
# let's convert Erica to erica
students.update_one({"name": "Erica"}, {"$set": {"name": "erica"}})
```




    <pymongo.results.UpdateResult at 0x10f345288>




```python
for res in students.find(filter={}):
    print(res)
```

    {'_id': ObjectId('5ea9d61a898be8b2d4711c81'), 'name': 'Heather', 'state': 'Texas'}
    {'_id': ObjectId('5ea9d652898be8b2d4711c82'), 'name': 'Thomas', 'state': 'California'}
    {'_id': ObjectId('5ea9d655898be8b2d4711c83'), 'name': 'erica', 'state': 'California'}
    {'_id': ObjectId('5ea9d69c898be8b2d4711c84'), 'name': 'Megan', 'county': 'Greater London'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c85'), 'name': 'leighanna', 'state': 'Michigan'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c86'), 'name': 'payson', 'state': 'Ohio'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c87'), 'name': 'edward', 'province': 'British Columbia'}



```python
# let's update everyone to live in California

students.update_many({"state": {"$exists": True}}, {"$set": {"state": "California"}})

```




    <pymongo.results.UpdateResult at 0x10f2c1dc8>




```python
for res in students.find(filter={}):
    print(res)
```

    {'_id': ObjectId('5ea9d61a898be8b2d4711c81'), 'name': 'Heather', 'state': 'California'}
    {'_id': ObjectId('5ea9d652898be8b2d4711c82'), 'name': 'Thomas', 'state': 'California'}
    {'_id': ObjectId('5ea9d655898be8b2d4711c83'), 'name': 'erica', 'state': 'California'}
    {'_id': ObjectId('5ea9d69c898be8b2d4711c84'), 'name': 'Megan', 'county': 'Greater London'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c85'), 'name': 'leighanna', 'state': 'California'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c86'), 'name': 'payson', 'state': 'California'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c87'), 'name': 'edward', 'province': 'British Columbia'}



```python
# move everyone to california that does not live in a US State
students.update_many({"state": {"$exists": False}}, {"$set": {"state": "California"}})
```




    <pymongo.results.UpdateResult at 0x10f163508>




```python
for res in students.find(filter={}):
    print(res)
```

    {'_id': ObjectId('5ea9d61a898be8b2d4711c81'), 'name': 'Heather', 'state': 'California'}
    {'_id': ObjectId('5ea9d652898be8b2d4711c82'), 'name': 'Thomas', 'state': 'California'}
    {'_id': ObjectId('5ea9d655898be8b2d4711c83'), 'name': 'erica', 'state': 'California'}
    {'_id': ObjectId('5ea9d69c898be8b2d4711c84'), 'name': 'Megan', 'county': 'Greater London', 'state': 'California'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c85'), 'name': 'leighanna', 'state': 'California'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c86'), 'name': 'payson', 'state': 'California'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c87'), 'name': 'edward', 'province': 'British Columbia', 'state': 'California'}



```python
# mood column -> 'eager to learn'

students.update_many({"name": {"$exists": True}}, {"$set": {"mood": "excited to learn!"}})
```




    <pymongo.results.UpdateResult at 0x10f2ce408>




```python
for res in students.find(filter={}):
    print(res)
```

    {'_id': ObjectId('5ea9d61a898be8b2d4711c81'), 'name': 'Heather', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d652898be8b2d4711c82'), 'name': 'Thomas', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d655898be8b2d4711c83'), 'name': 'erica', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d69c898be8b2d4711c84'), 'name': 'Megan', 'county': 'Greater London', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c85'), 'name': 'leighanna', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c86'), 'name': 'payson', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c87'), 'name': 'edward', 'province': 'British Columbia', 'state': 'California', 'mood': 'excited to learn!'}



```python
students.delete_one(filter={"name": "leighanna"})
```




    <pymongo.results.DeleteResult at 0x10f1f5fc8>




```python
for res in students.find(filter={}):
    print(res)
```

    {'_id': ObjectId('5ea9d61a898be8b2d4711c81'), 'name': 'Heather', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d652898be8b2d4711c82'), 'name': 'Thomas', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d655898be8b2d4711c83'), 'name': 'erica', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d69c898be8b2d4711c84'), 'name': 'Megan', 'county': 'Greater London', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c86'), 'name': 'payson', 'state': 'California', 'mood': 'excited to learn!'}
    {'_id': ObjectId('5ea9d772898be8b2d4711c87'), 'name': 'edward', 'province': 'British Columbia', 'state': 'California', 'mood': 'excited to learn!'}



```python
students.insert_one({"name": "Alex", "state": "California"})
```




    <pymongo.results.InsertOneResult at 0x10f4a7988>




```python
students.insert_one({"name": "Jonny", "county": None})
```




    <pymongo.results.InsertOneResult at 0x10f46ce08>




```python
students.delete_many({"name": {"$regex": ".*a.*", "$options": "i"}})
```




    <pymongo.results.DeleteResult at 0x10f2e9dc8>




```python
for res in students.find():
    print(res)
```

    {'_id': ObjectId('5ea9de25898be8b2d4711c89'), 'name': 'Jonny', 'county': None}
    {'_id': ObjectId('5ea9ded3898be8b2d4711c8a'), 'name': 'Heather', 'state': 'Texas'}
    {'_id': ObjectId('5ea9ded5898be8b2d4711c8b'), 'name': 'Thomas', 'state': 'California'}
    {'_id': ObjectId('5ea9ded7898be8b2d4711c8c'), 'name': 'Erica', 'state': 'California'}
    {'_id': ObjectId('5ea9ded9898be8b2d4711c8d'), 'name': 'Megan', 'county': 'Greater London'}
    {'_id': ObjectId('5ea9dedb898be8b2d4711c8e'), 'name': 'leighanna', 'state': 'Michigan'}
    {'_id': ObjectId('5ea9dedb898be8b2d4711c8f'), 'name': 'payson', 'state': 'Ohio'}
    {'_id': ObjectId('5ea9dedb898be8b2d4711c90'), 'name': 'edward', 'province': 'British Columbia'}


# How do we turn this into a dataframe?


```python
import pandas as pd
```


```python
df = pd.DataFrame(students.find(filter={}))
df
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
      <th>county</th>
      <th>state</th>
      <th>province</th>
      <th>doc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>5ea9de25898be8b2d4711c89</td>
      <td>Jonny</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>1</td>
      <td>5ea9ded3898be8b2d4711c8a</td>
      <td>Heather</td>
      <td>NaN</td>
      <td>Texas</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>2</td>
      <td>5ea9ded5898be8b2d4711c8b</td>
      <td>Thomas</td>
      <td>NaN</td>
      <td>California</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>3</td>
      <td>5ea9ded7898be8b2d4711c8c</td>
      <td>Erica</td>
      <td>NaN</td>
      <td>California</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5ea9ded9898be8b2d4711c8d</td>
      <td>Megan</td>
      <td>Greater London</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>5</td>
      <td>5ea9dedb898be8b2d4711c8e</td>
      <td>leighanna</td>
      <td>NaN</td>
      <td>Michigan</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>6</td>
      <td>5ea9dedb898be8b2d4711c8f</td>
      <td>payson</td>
      <td>NaN</td>
      <td>Ohio</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>7</td>
      <td>5ea9dedb898be8b2d4711c90</td>
      <td>edward</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>British Columbia</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>8</td>
      <td>5ea9e081898be8b2d4711c91</td>
      <td>32</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>9</td>
      <td>5ea9e0a7898be8b2d4711c92</td>
      <td>{'name': 32}</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>10</td>
      <td>5ea9e12e898be8b2d4711c93</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[{'name': 'Jonny', 'county': None}, {'name': '...</td>
    </tr>
  </tbody>
</table>
</div>




```python
students.insert_one({"doc": [{'name': 'Jonny', 'county': None},
{'name': 'Heather', 'state': 'Texas'},
{'name': 'Thomas', 'state': 'California'},
{'name': 'Erica', 'state': 'California'},
{'name': 'Megan', 'county': 'Greater London'},
{'name': 'leighanna', 'state': 'Michigan'},
{'name': 'payson', 'state': 'Ohio'},
{'name': 'edward', 'province': 'British Columbia'}]})
```




    <pymongo.results.InsertOneResult at 0x120877a88>




```python
students.insert_one({"name": 32})
```




    <pymongo.results.InsertOneResult at 0x1206bc948>




```python
students.insert_one({"name": {"name": 32}})
```




    <pymongo.results.InsertOneResult at 0x1206d1188>




```python

```


```python

```


```python
tweets_db = client['tweets']
```


```python
tweets_db.list_collection_names()
```




    ['facebook_tweets',
     'got_collection',
     'queer_eye_kanye_tweets',
     'python',
     'dating_tweets',
     'lilNasX',
     'finals_2019',
     'general',
     'donald_tweets']



# Let's delete the current 'new_db'



```python
# your MongoClient object does db stuff
print(client.list_database_names())
client.drop_database('new_db')
print(client.list_database_names())
```

    ['admin', 'config', 'ebay', 'headstorm_client_db', 'local', 'music_tweets', 'new_db', 'tweets']
    ['admin', 'config', 'ebay', 'headstorm_client_db', 'local', 'music_tweets', 'tweets']


# Let's recreate it and use it in our study group! :) 


```python
new_db = client['new_db']
```


```python
# let's see if we created our new_db successfully! 

client.list_database_names()
```




    ['admin',
     'config',
     'ebay',
     'headstorm_client_db',
     'local',
     'music_tweets',
     'tweets']




```python
student_collection = new_db['student_collection']
```


```python
client.list_database_names()
```




    ['admin',
     'config',
     'ebay',
     'headstorm_client_db',
     'local',
     'music_tweets',
     'tweets']




```python
maria = {"name": "maria", "state": "california", "favorite_tv_show": "greys anatomy"}
```


```python
maria
```




    {'name': 'maria', 'state': 'california', 'favorite_tv_show': 'greys anatomy'}




```python
student_collection.insert_one(maria)
```




    <pymongo.results.InsertOneResult at 0x1a2794f0c8>




```python
client.list_database_names()
```




    ['admin',
     'config',
     'ebay',
     'headstorm_client_db',
     'local',
     'music_tweets',
     'new_db',
     'tweets']




```python
# you have to add a document to a collection in a new_db in order for the new_db to get written into memory


```


```python
andy = {"name": "andy", "state": "new york", "favorite_tv_show": "game of thrones"}
forest = {"name": "forest", "state": "hawaii", "favorite_tv_show": "rick and morty"}
shirl = {"name": "shirl", "state": "georgia", "favorite_tv_show": "criminal minds"}
leighanna = {"name": "leighanna", "state": "florida", "favorite_tv_show": "american horror story"}
kelvin = {"name": "kelvin", "state": "florida", "favorite_tv_show": "the witcher"}
jamel = {"name": "jamel", "state": None, "favorite_tv_show": ":)"}
```


```python
lst = [andy, forest, shirl, leighanna, kelvin, jamel]
lst
```




    [{'name': 'andy', 'state': 'new york', 'favorite_tv_show': 'game of thrones'},
     {'name': 'forest', 'state': 'hawaii', 'favorite_tv_show': 'rick and morty'},
     {'name': 'shirl', 'state': 'georgia', 'favorite_tv_show': 'criminal minds'},
     {'name': 'leighanna',
      'state': 'florida',
      'favorite_tv_show': 'american horror story'},
     {'name': 'kelvin', 'state': 'florida', 'favorite_tv_show': 'the witcher'},
     {'name': 'jamel', 'state': None, 'favorite_tv_show': ':)'}]




```python
student_collection.insert_many(lst)
```




    <pymongo.results.InsertManyResult at 0x1a2726cf48>




```python
student_collection.count()
```

    /Users/rafael/anaconda3/envs/flatiron-env/lib/python3.6/site-packages/ipykernel_launcher.py:1: DeprecationWarning: count is deprecated. Use estimated_document_count or count_documents instead. Please note that $where must be replaced by $expr, $near must be replaced by $geoWithin with $center, and $nearSphere must be replaced by $geoWithin with $centerSphere
      """Entry point for launching an IPython kernel.





    7




```python
list(student_collection.find({"state": "florida"}))
```




    [{'_id': ObjectId('5e163fc149d25d1b4651bfcb'),
      'name': 'leighanna',
      'state': 'florida',
      'favorite_tv_show': 'american horror story'},
     {'_id': ObjectId('5e163fc149d25d1b4651bfcc'),
      'name': 'kelvin',
      'state': 'florida',
      'favorite_tv_show': 'the witcher'}]




```python
for florida_student in student_collection.find({"state": "florida"}):
    print(florida_student)
```

    {'_id': ObjectId('5e163fc149d25d1b4651bfcb'), 'name': 'leighanna', 'state': 'florida', 'favorite_tv_show': 'american horror story'}
    {'_id': ObjectId('5e163fc149d25d1b4651bfcc'), 'name': 'kelvin', 'state': 'florida', 'favorite_tv_show': 'the witcher'}


# Let's load in our students into a dataframe



```python
df = pd.DataFrame(lst)
df
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
      <th>state</th>
      <th>favorite_tv_show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>andy</td>
      <td>new york</td>
      <td>game of thrones</td>
    </tr>
    <tr>
      <td>1</td>
      <td>forest</td>
      <td>hawaii</td>
      <td>rick and morty</td>
    </tr>
    <tr>
      <td>2</td>
      <td>shirl</td>
      <td>georgia</td>
      <td>criminal minds</td>
    </tr>
    <tr>
      <td>3</td>
      <td>leighanna</td>
      <td>florida</td>
      <td>american horror story</td>
    </tr>
    <tr>
      <td>4</td>
      <td>kelvin</td>
      <td>florida</td>
      <td>the witcher</td>
    </tr>
    <tr>
      <td>5</td>
      <td>jamel</td>
      <td>None</td>
      <td>:)</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = pd.DataFrame(list(student_collection.find()))
df
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
      <th>state</th>
      <th>favorite_tv_show</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>5e163e3149d25d1b4651bfc7</td>
      <td>maria</td>
      <td>california</td>
      <td>greys anatomy</td>
    </tr>
    <tr>
      <td>1</td>
      <td>5e163fc149d25d1b4651bfc8</td>
      <td>andy</td>
      <td>new york</td>
      <td>game of thrones</td>
    </tr>
    <tr>
      <td>2</td>
      <td>5e163fc149d25d1b4651bfc9</td>
      <td>forest</td>
      <td>hawaii</td>
      <td>rick and morty</td>
    </tr>
    <tr>
      <td>3</td>
      <td>5e163fc149d25d1b4651bfca</td>
      <td>shirl</td>
      <td>georgia</td>
      <td>criminal minds</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5e163fc149d25d1b4651bfcb</td>
      <td>leighanna</td>
      <td>florida</td>
      <td>american horror story</td>
    </tr>
    <tr>
      <td>5</td>
      <td>5e163fc149d25d1b4651bfcc</td>
      <td>kelvin</td>
      <td>florida</td>
      <td>the witcher</td>
    </tr>
    <tr>
      <td>6</td>
      <td>5e163fc149d25d1b4651bfcd</td>
      <td>jamel</td>
      <td>None</td>
      <td>:)</td>
    </tr>
  </tbody>
</table>
</div>



### Let's insert date using an API


```python
with open("/Users/rafael/.ssh/twitter_app00.json") as f: # with will close the file after you exit the process
    d = json.load(f) # d is going to be a 
print(d.keys())
```

    dict_keys(['consumer_key', 'consumer_secret', 'access_token', 'access_token_secret'])


# Let's start a twitter client


```python
oauth = tweepy.OAuthHandler(consumer_key = d["consumer_key"], consumer_secret = d["consumer_secret"])
```


```python
oauth.set_access_token(key=d["access_token"], secret=d["access_token_secret"])
```


```python
api = tweepy.API(auth_handler=oauth)
```


```python
barkpark_collection = new_db['barkpark']

for tw in api.search("#barkparkupdates"):
    barkpark_collection.insert_one(tw._json) # insert these tweets into mongodb
```


```python
list(barkpark_collection.find())
```




    [{'_id': ObjectId('5e16454249d25d1b4651bfce'),
      'created_at': 'Wed Jan 08 13:37:02 +0000 2020',
      'id': 1214903842808246272,
      'id_str': '1214903842808246272',
      'text': '#barkparkupdates Wednesday January 8- nice frozen trails. https://t.co/OwVh6NkK6u',
      'truncated': False,
      'entities': {'hashtags': [{'text': 'barkparkupdates', 'indices': [0, 16]}],
       'symbols': [],
       'user_mentions': [],
       'urls': [],
       'media': [{'id': 1214903835040395264,
         'id_str': '1214903835040395264',
         'indices': [58, 81],
         'media_url': 'http://pbs.twimg.com/media/ENw0mePXsAAcGc9.jpg',
         'media_url_https': 'https://pbs.twimg.com/media/ENw0mePXsAAcGc9.jpg',
         'url': 'https://t.co/OwVh6NkK6u',
         'display_url': 'pic.twitter.com/OwVh6NkK6u',
         'expanded_url': 'https://twitter.com/LKthedog/status/1214903842808246272/photo/1',
         'type': 'photo',
         'sizes': {'thumb': {'w': 150, 'h': 150, 'resize': 'crop'},
          'small': {'w': 510, 'h': 680, 'resize': 'fit'},
          'medium': {'w': 900, 'h': 1200, 'resize': 'fit'},
          'large': {'w': 1536, 'h': 2048, 'resize': 'fit'}}}]},
      'extended_entities': {'media': [{'id': 1214903835040395264,
         'id_str': '1214903835040395264',
         'indices': [58, 81],
         'media_url': 'http://pbs.twimg.com/media/ENw0mePXsAAcGc9.jpg',
         'media_url_https': 'https://pbs.twimg.com/media/ENw0mePXsAAcGc9.jpg',
         'url': 'https://t.co/OwVh6NkK6u',
         'display_url': 'pic.twitter.com/OwVh6NkK6u',
         'expanded_url': 'https://twitter.com/LKthedog/status/1214903842808246272/photo/1',
         'type': 'photo',
         'sizes': {'thumb': {'w': 150, 'h': 150, 'resize': 'crop'},
          'small': {'w': 510, 'h': 680, 'resize': 'fit'},
          'medium': {'w': 900, 'h': 1200, 'resize': 'fit'},
          'large': {'w': 1536, 'h': 2048, 'resize': 'fit'}}}]},
      'metadata': {'iso_language_code': 'en', 'result_type': 'recent'},
      'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
      'in_reply_to_status_id': None,
      'in_reply_to_status_id_str': None,
      'in_reply_to_user_id': None,
      'in_reply_to_user_id_str': None,
      'in_reply_to_screen_name': None,
      'user': {'id': 1096617690133860352,
       'id_str': '1096617690133860352',
       'name': 'Lazlo the dog',
       'screen_name': 'LKthedog',
       'location': 'Tonawanda, NY',
       'description': "Just a dog(the best dog,I'm told)",
       'url': None,
       'entities': {'description': {'urls': []}},
       'protected': False,
       'followers_count': 17,
       'friends_count': 16,
       'listed_count': 0,
       'created_at': 'Sat Feb 16 03:50:06 +0000 2019',
       'favourites_count': 145,
       'utc_offset': None,
       'time_zone': None,
       'geo_enabled': False,
       'verified': False,
       'statuses_count': 82,
       'lang': None,
       'contributors_enabled': False,
       'is_translator': False,
       'is_translation_enabled': False,
       'profile_background_color': 'F5F8FA',
       'profile_background_image_url': None,
       'profile_background_image_url_https': None,
       'profile_background_tile': False,
       'profile_image_url': 'http://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1096617690133860352/1550290173',
       'profile_link_color': '1DA1F2',
       'profile_sidebar_border_color': 'C0DEED',
       'profile_sidebar_fill_color': 'DDEEF6',
       'profile_text_color': '333333',
       'profile_use_background_image': True,
       'has_extended_profile': False,
       'default_profile': True,
       'default_profile_image': False,
       'following': False,
       'follow_request_sent': False,
       'notifications': False,
       'translator_type': 'none'},
      'geo': None,
      'coordinates': None,
      'place': None,
      'contributors': None,
      'is_quote_status': False,
      'retweet_count': 0,
      'favorite_count': 1,
      'favorited': False,
      'retweeted': False,
      'possibly_sensitive': False,
      'lang': 'en'},
     {'_id': ObjectId('5e16454249d25d1b4651bfcf'),
      'created_at': 'Tue Jan 07 13:31:55 +0000 2020',
      'id': 1214540166989320193,
      'id_str': '1214540166989320193',
      'text': '#barkparkupdates Tuesday January 7- nice frozen trails. Bridge is crunchy icy be careful walking. Come early if you can.',
      'truncated': False,
      'entities': {'hashtags': [{'text': 'barkparkupdates', 'indices': [0, 16]}],
       'symbols': [],
       'user_mentions': [],
       'urls': []},
      'metadata': {'iso_language_code': 'en', 'result_type': 'recent'},
      'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
      'in_reply_to_status_id': None,
      'in_reply_to_status_id_str': None,
      'in_reply_to_user_id': None,
      'in_reply_to_user_id_str': None,
      'in_reply_to_screen_name': None,
      'user': {'id': 1096617690133860352,
       'id_str': '1096617690133860352',
       'name': 'Lazlo the dog',
       'screen_name': 'LKthedog',
       'location': 'Tonawanda, NY',
       'description': "Just a dog(the best dog,I'm told)",
       'url': None,
       'entities': {'description': {'urls': []}},
       'protected': False,
       'followers_count': 17,
       'friends_count': 16,
       'listed_count': 0,
       'created_at': 'Sat Feb 16 03:50:06 +0000 2019',
       'favourites_count': 145,
       'utc_offset': None,
       'time_zone': None,
       'geo_enabled': False,
       'verified': False,
       'statuses_count': 82,
       'lang': None,
       'contributors_enabled': False,
       'is_translator': False,
       'is_translation_enabled': False,
       'profile_background_color': 'F5F8FA',
       'profile_background_image_url': None,
       'profile_background_image_url_https': None,
       'profile_background_tile': False,
       'profile_image_url': 'http://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1096617690133860352/1550290173',
       'profile_link_color': '1DA1F2',
       'profile_sidebar_border_color': 'C0DEED',
       'profile_sidebar_fill_color': 'DDEEF6',
       'profile_text_color': '333333',
       'profile_use_background_image': True,
       'has_extended_profile': False,
       'default_profile': True,
       'default_profile_image': False,
       'following': False,
       'follow_request_sent': False,
       'notifications': False,
       'translator_type': 'none'},
      'geo': None,
      'coordinates': None,
      'place': None,
      'contributors': None,
      'is_quote_status': False,
      'retweet_count': 0,
      'favorite_count': 0,
      'favorited': False,
      'retweeted': False,
      'lang': 'en'},
     {'_id': ObjectId('5e16454249d25d1b4651bfd0'),
      'created_at': 'Mon Jan 06 14:17:55 +0000 2020',
      'id': 1214189355528572930,
      'id_str': '1214189355528572930',
      'text': '#barkparkupdates Monday January 6- muddy trails under wet snow',
      'truncated': False,
      'entities': {'hashtags': [{'text': 'barkparkupdates', 'indices': [0, 16]}],
       'symbols': [],
       'user_mentions': [],
       'urls': []},
      'metadata': {'iso_language_code': 'en', 'result_type': 'recent'},
      'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
      'in_reply_to_status_id': None,
      'in_reply_to_status_id_str': None,
      'in_reply_to_user_id': None,
      'in_reply_to_user_id_str': None,
      'in_reply_to_screen_name': None,
      'user': {'id': 1096617690133860352,
       'id_str': '1096617690133860352',
       'name': 'Lazlo the dog',
       'screen_name': 'LKthedog',
       'location': 'Tonawanda, NY',
       'description': "Just a dog(the best dog,I'm told)",
       'url': None,
       'entities': {'description': {'urls': []}},
       'protected': False,
       'followers_count': 17,
       'friends_count': 16,
       'listed_count': 0,
       'created_at': 'Sat Feb 16 03:50:06 +0000 2019',
       'favourites_count': 145,
       'utc_offset': None,
       'time_zone': None,
       'geo_enabled': False,
       'verified': False,
       'statuses_count': 82,
       'lang': None,
       'contributors_enabled': False,
       'is_translator': False,
       'is_translation_enabled': False,
       'profile_background_color': 'F5F8FA',
       'profile_background_image_url': None,
       'profile_background_image_url_https': None,
       'profile_background_tile': False,
       'profile_image_url': 'http://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1096617690133860352/1550290173',
       'profile_link_color': '1DA1F2',
       'profile_sidebar_border_color': 'C0DEED',
       'profile_sidebar_fill_color': 'DDEEF6',
       'profile_text_color': '333333',
       'profile_use_background_image': True,
       'has_extended_profile': False,
       'default_profile': True,
       'default_profile_image': False,
       'following': False,
       'follow_request_sent': False,
       'notifications': False,
       'translator_type': 'none'},
      'geo': None,
      'coordinates': None,
      'place': None,
      'contributors': None,
      'is_quote_status': False,
      'retweet_count': 0,
      'favorite_count': 0,
      'favorited': False,
      'retweeted': False,
      'lang': 'en'},
     {'_id': ObjectId('5e16454249d25d1b4651bfd1'),
      'created_at': 'Sun Jan 05 13:33:38 +0000 2020',
      'id': 1213815825049804800,
      'id_str': '1213815825049804800',
      'text': '#barkparkupdates Sunday January5- fresh snow over soft ground. Clear and crisp so far',
      'truncated': False,
      'entities': {'hashtags': [{'text': 'barkparkupdates', 'indices': [0, 16]}],
       'symbols': [],
       'user_mentions': [],
       'urls': []},
      'metadata': {'iso_language_code': 'en', 'result_type': 'recent'},
      'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
      'in_reply_to_status_id': None,
      'in_reply_to_status_id_str': None,
      'in_reply_to_user_id': None,
      'in_reply_to_user_id_str': None,
      'in_reply_to_screen_name': None,
      'user': {'id': 1096617690133860352,
       'id_str': '1096617690133860352',
       'name': 'Lazlo the dog',
       'screen_name': 'LKthedog',
       'location': 'Tonawanda, NY',
       'description': "Just a dog(the best dog,I'm told)",
       'url': None,
       'entities': {'description': {'urls': []}},
       'protected': False,
       'followers_count': 17,
       'friends_count': 16,
       'listed_count': 0,
       'created_at': 'Sat Feb 16 03:50:06 +0000 2019',
       'favourites_count': 145,
       'utc_offset': None,
       'time_zone': None,
       'geo_enabled': False,
       'verified': False,
       'statuses_count': 82,
       'lang': None,
       'contributors_enabled': False,
       'is_translator': False,
       'is_translation_enabled': False,
       'profile_background_color': 'F5F8FA',
       'profile_background_image_url': None,
       'profile_background_image_url_https': None,
       'profile_background_tile': False,
       'profile_image_url': 'http://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1096617690133860352/1550290173',
       'profile_link_color': '1DA1F2',
       'profile_sidebar_border_color': 'C0DEED',
       'profile_sidebar_fill_color': 'DDEEF6',
       'profile_text_color': '333333',
       'profile_use_background_image': True,
       'has_extended_profile': False,
       'default_profile': True,
       'default_profile_image': False,
       'following': False,
       'follow_request_sent': False,
       'notifications': False,
       'translator_type': 'none'},
      'geo': None,
      'coordinates': None,
      'place': None,
      'contributors': None,
      'is_quote_status': False,
      'retweet_count': 0,
      'favorite_count': 0,
      'favorited': False,
      'retweeted': False,
      'lang': 'en'},
     {'_id': ObjectId('5e16454249d25d1b4651bfd2'),
      'created_at': 'Sat Jan 04 13:34:31 +0000 2020',
      'id': 1213453657989156864,
      'id_str': '1213453657989156864',
      'text': '#barkparkupdates Saturday January 4 trails are slushy mud, but not too bad. Come enjoy the fresh snow!',
      'truncated': False,
      'entities': {'hashtags': [{'text': 'barkparkupdates', 'indices': [0, 16]}],
       'symbols': [],
       'user_mentions': [],
       'urls': []},
      'metadata': {'iso_language_code': 'en', 'result_type': 'recent'},
      'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
      'in_reply_to_status_id': None,
      'in_reply_to_status_id_str': None,
      'in_reply_to_user_id': None,
      'in_reply_to_user_id_str': None,
      'in_reply_to_screen_name': None,
      'user': {'id': 1096617690133860352,
       'id_str': '1096617690133860352',
       'name': 'Lazlo the dog',
       'screen_name': 'LKthedog',
       'location': 'Tonawanda, NY',
       'description': "Just a dog(the best dog,I'm told)",
       'url': None,
       'entities': {'description': {'urls': []}},
       'protected': False,
       'followers_count': 17,
       'friends_count': 16,
       'listed_count': 0,
       'created_at': 'Sat Feb 16 03:50:06 +0000 2019',
       'favourites_count': 145,
       'utc_offset': None,
       'time_zone': None,
       'geo_enabled': False,
       'verified': False,
       'statuses_count': 82,
       'lang': None,
       'contributors_enabled': False,
       'is_translator': False,
       'is_translation_enabled': False,
       'profile_background_color': 'F5F8FA',
       'profile_background_image_url': None,
       'profile_background_image_url_https': None,
       'profile_background_tile': False,
       'profile_image_url': 'http://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1096617690133860352/1550290173',
       'profile_link_color': '1DA1F2',
       'profile_sidebar_border_color': 'C0DEED',
       'profile_sidebar_fill_color': 'DDEEF6',
       'profile_text_color': '333333',
       'profile_use_background_image': True,
       'has_extended_profile': False,
       'default_profile': True,
       'default_profile_image': False,
       'following': False,
       'follow_request_sent': False,
       'notifications': False,
       'translator_type': 'none'},
      'geo': None,
      'coordinates': None,
      'place': None,
      'contributors': None,
      'is_quote_status': False,
      'retweet_count': 0,
      'favorite_count': 0,
      'favorited': False,
      'retweeted': False,
      'lang': 'en'},
     {'_id': ObjectId('5e16454249d25d1b4651bfd3'),
      'created_at': 'Fri Jan 03 13:33:47 +0000 2020',
      'id': 1213091085012082688,
      'id_str': '1213091085012082688',
      'text': '#barkparkupdates Friday January 3- still no snow or ice, trails are good, ground is soft.',
      'truncated': False,
      'entities': {'hashtags': [{'text': 'barkparkupdates', 'indices': [0, 16]}],
       'symbols': [],
       'user_mentions': [],
       'urls': []},
      'metadata': {'iso_language_code': 'en', 'result_type': 'recent'},
      'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
      'in_reply_to_status_id': None,
      'in_reply_to_status_id_str': None,
      'in_reply_to_user_id': None,
      'in_reply_to_user_id_str': None,
      'in_reply_to_screen_name': None,
      'user': {'id': 1096617690133860352,
       'id_str': '1096617690133860352',
       'name': 'Lazlo the dog',
       'screen_name': 'LKthedog',
       'location': 'Tonawanda, NY',
       'description': "Just a dog(the best dog,I'm told)",
       'url': None,
       'entities': {'description': {'urls': []}},
       'protected': False,
       'followers_count': 17,
       'friends_count': 16,
       'listed_count': 0,
       'created_at': 'Sat Feb 16 03:50:06 +0000 2019',
       'favourites_count': 145,
       'utc_offset': None,
       'time_zone': None,
       'geo_enabled': False,
       'verified': False,
       'statuses_count': 82,
       'lang': None,
       'contributors_enabled': False,
       'is_translator': False,
       'is_translation_enabled': False,
       'profile_background_color': 'F5F8FA',
       'profile_background_image_url': None,
       'profile_background_image_url_https': None,
       'profile_background_tile': False,
       'profile_image_url': 'http://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1096617690133860352/1550290173',
       'profile_link_color': '1DA1F2',
       'profile_sidebar_border_color': 'C0DEED',
       'profile_sidebar_fill_color': 'DDEEF6',
       'profile_text_color': '333333',
       'profile_use_background_image': True,
       'has_extended_profile': False,
       'default_profile': True,
       'default_profile_image': False,
       'following': False,
       'follow_request_sent': False,
       'notifications': False,
       'translator_type': 'none'},
      'geo': None,
      'coordinates': None,
      'place': None,
      'contributors': None,
      'is_quote_status': False,
      'retweet_count': 0,
      'favorite_count': 0,
      'favorited': False,
      'retweeted': False,
      'lang': 'en'},
     {'_id': ObjectId('5e16454249d25d1b4651bfd4'),
      'created_at': 'Thu Jan 02 13:55:20 +0000 2020',
      'id': 1212734120188620800,
      'id_str': '1212734120188620800',
      'text': '#barkparkupdates    Thursday January 2- cold morning, frozen conditions for the path.',
      'truncated': False,
      'entities': {'hashtags': [{'text': 'barkparkupdates', 'indices': [0, 16]}],
       'symbols': [],
       'user_mentions': [],
       'urls': []},
      'metadata': {'iso_language_code': 'en', 'result_type': 'recent'},
      'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
      'in_reply_to_status_id': None,
      'in_reply_to_status_id_str': None,
      'in_reply_to_user_id': None,
      'in_reply_to_user_id_str': None,
      'in_reply_to_screen_name': None,
      'user': {'id': 1096617690133860352,
       'id_str': '1096617690133860352',
       'name': 'Lazlo the dog',
       'screen_name': 'LKthedog',
       'location': 'Tonawanda, NY',
       'description': "Just a dog(the best dog,I'm told)",
       'url': None,
       'entities': {'description': {'urls': []}},
       'protected': False,
       'followers_count': 17,
       'friends_count': 16,
       'listed_count': 0,
       'created_at': 'Sat Feb 16 03:50:06 +0000 2019',
       'favourites_count': 145,
       'utc_offset': None,
       'time_zone': None,
       'geo_enabled': False,
       'verified': False,
       'statuses_count': 82,
       'lang': None,
       'contributors_enabled': False,
       'is_translator': False,
       'is_translation_enabled': False,
       'profile_background_color': 'F5F8FA',
       'profile_background_image_url': None,
       'profile_background_image_url_https': None,
       'profile_background_tile': False,
       'profile_image_url': 'http://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1096617690133860352/1550290173',
       'profile_link_color': '1DA1F2',
       'profile_sidebar_border_color': 'C0DEED',
       'profile_sidebar_fill_color': 'DDEEF6',
       'profile_text_color': '333333',
       'profile_use_background_image': True,
       'has_extended_profile': False,
       'default_profile': True,
       'default_profile_image': False,
       'following': False,
       'follow_request_sent': False,
       'notifications': False,
       'translator_type': 'none'},
      'geo': None,
      'coordinates': None,
      'place': None,
      'contributors': None,
      'is_quote_status': False,
      'retweet_count': 0,
      'favorite_count': 0,
      'favorited': False,
      'retweeted': False,
      'lang': 'en'},
     {'_id': ObjectId('5e16454249d25d1b4651bfd5'),
      'created_at': 'Wed Jan 01 14:22:48 +0000 2020',
      'id': 1212378645509148679,
      'id_str': '1212378645509148679',
      'text': '#barkparkupdates Wednesday January 1-happy new year to all! The park is frozen so easy going for everyone!',
      'truncated': False,
      'entities': {'hashtags': [{'text': 'barkparkupdates', 'indices': [0, 16]}],
       'symbols': [],
       'user_mentions': [],
       'urls': []},
      'metadata': {'iso_language_code': 'en', 'result_type': 'recent'},
      'source': '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
      'in_reply_to_status_id': None,
      'in_reply_to_status_id_str': None,
      'in_reply_to_user_id': None,
      'in_reply_to_user_id_str': None,
      'in_reply_to_screen_name': None,
      'user': {'id': 1096617690133860352,
       'id_str': '1096617690133860352',
       'name': 'Lazlo the dog',
       'screen_name': 'LKthedog',
       'location': 'Tonawanda, NY',
       'description': "Just a dog(the best dog,I'm told)",
       'url': None,
       'entities': {'description': {'urls': []}},
       'protected': False,
       'followers_count': 17,
       'friends_count': 16,
       'listed_count': 0,
       'created_at': 'Sat Feb 16 03:50:06 +0000 2019',
       'favourites_count': 145,
       'utc_offset': None,
       'time_zone': None,
       'geo_enabled': False,
       'verified': False,
       'statuses_count': 82,
       'lang': None,
       'contributors_enabled': False,
       'is_translator': False,
       'is_translation_enabled': False,
       'profile_background_color': 'F5F8FA',
       'profile_background_image_url': None,
       'profile_background_image_url_https': None,
       'profile_background_tile': False,
       'profile_image_url': 'http://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_image_url_https': 'https://pbs.twimg.com/profile_images/1096622575466266624/EZP-IHXH_normal.jpg',
       'profile_banner_url': 'https://pbs.twimg.com/profile_banners/1096617690133860352/1550290173',
       'profile_link_color': '1DA1F2',
       'profile_sidebar_border_color': 'C0DEED',
       'profile_sidebar_fill_color': 'DDEEF6',
       'profile_text_color': '333333',
       'profile_use_background_image': True,
       'has_extended_profile': False,
       'default_profile': True,
       'default_profile_image': False,
       'following': False,
       'follow_request_sent': False,
       'notifications': False,
       'translator_type': 'none'},
      'geo': None,
      'coordinates': None,
      'place': None,
      'contributors': None,
      'is_quote_status': False,
      'retweet_count': 0,
      'favorite_count': 1,
      'favorited': False,
      'retweeted': False,
      'lang': 'en'}]




```python

```
