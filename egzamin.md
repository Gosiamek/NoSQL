#Aggregation Pipeline

Do wykonania tego zadania użyję bazy danych redditów wykorzystaną we wcześniejszym zadaniu.

## 1. Wyświetlenie przykładowego rekordu.
<br>
Aby wyświetlić przykładowy rekord używamy następującego zapytania do bazy.

```javascript
db.RCOLL.findOne()
```
<br>
Wygląd przykładowego rekordu możemy zobaczyć poniżej.
```javascript
{
        "_id" : ObjectId("564b7e299de69b31fcf05ae8"),
        "body" : "Very fast, thank you!",
        "created_utc" : "1420070400",
        "downs" : 0,
        "author" : "Mastersimpson",
        "score" : 2,
        "id" : "cnas8zz",
        "distinguished" : null,
        "archived" : false,
        "score_hidden" : false,
        "name" : "t1_cnas8zz",
        "link_id" : "t3_2qm5bi",
        "author_flair_css_class" : null,
        "gilded" : 0,
        "author_flair_text" : null,
        "retrieved_on" : 1425124282,
        "ups" : 2,
        "controversiality" : 0,
        "edited" : false,
        "subreddit_id" : "t5_2y51u",
        "parent_id" : "t3_2qm5bi",
        "subreddit" : "freedonuts"
}
```

##2. Wykonywanie zapytań aggregation do bazy danych.
<br>
####1) Zapytanie o ilość najbardziej kontrowersyjnych komentarzy.

JAVASCRIPT
```javascript
db.RCOLL.aggregate([{$group:{_id:"$author", controversiality: {$sum: "$controversiality" }}}, 
                    {$sort: {controversiality:-1}}, {$limit: 4} ], 
                    {allowDiskUse:true}
                  )
```
<br>
Użycie procesorów ukazane na 7 rdzeniach podczas wykonywania agregacji.
![](http://i.imgur.com/yIs0rTe.jpg)
<br>
Użycie pamięci RAM podczas wykonywania agregacji.
![](http://i.imgur.com/qE37cOc.jpg)
<br>
Użycie dysku podczas wykonywania agregacji.
![](http://i.imgur.com/jOu7IYS.jpg)
<br>

Wynik zapytania o ilość najbardziej kontrowersyjnych komentarzy.

```javascript
{ "_id" : "-------------------x", "controversiality" : 0 }
{ "_id" : "---------------_-", "controversiality" : 0 }
{ "_id" : "---", "controversiality" : 0 }
{ "_id" : "------------------__", "controversiality" : 0 }
```
<br>
PYMONGO
```javascript
import pymongo
client = pymongo.MongoClient("localhost", 27017)
db = client.RCOLL
db.RCOLL.aggregate([{$group:{_id:"$author", controversiality: {$sum: "$controversiality" }}}, 
                    {$sort: {controversiality:-1}}, {$limit: 4} ], 
                    {allowDiskUse:true}
                  )
```
<br>
####2) Zapytanie o ilość najgorszych wpisów u trzech najgorszych autorów.

JAVASCRIPT
```javascript
db.RCOLL.aggregate([ { $group: { _id: "$author", score: {$sum: "$score" } } },
                     { $sort: {score: 1} }, { $limit: 3} ], 
                     {allowDiskUse: true}
                   )
```
<br>
Wynik zapytania o ilość najgorszych wpisów u trzech najgorszych autorów.
```javascript
{ "_id" : "wutshappening", "score" : -8990 }
{ "_id" : "dwimback", "score" : -5241 }
{ "_id" : "_PM_ME_YOUR_PROLAPSE", "score" : -5043 }
```
<br>
PYMONGO
```javascript
import pymongo
client = pymongo.MongoClient("localhost", 27017)
db = client.RCOLL
db.RCOLL.aggregate([ { $group: { _id: "$author", score: {$sum: "$score" } } },
                     { $sort: {score: 1} }, { $limit: 3} ], 
                     {allowDiskUse: true}
                   )
```
<br>
####3) Zapytanie o ilość postów, które nie byly edytowane.

JAVASCRIPT
```javascript
db.RCOLL.aggregate ( [ {$match: {edited: false} }, 
                       {$group: { _id: null, count: 
                       {$sum: 1}}} ]
                   )
```
<br>
Wydajność zasobów podczas wykonywania agregacji.
![](http://i.imgur.com/1wjR9JW.jpg)
<br>
Wynik zapytania o ilość postów, które nie byly edytowane.

```javascript
{ "_id" : null, "count" : 52233268 }
```
<br>
PYMONGO
```javascript
import pymongo
client = pymongo.MongoClient("localhost", 27017)
db = client.RCOLL
db.RCOLL.aggregate ( [ {$match: {edited: false} }, 
                       {$group: { _id: null, count: 
                       {$sum: 1}}} ]
                   )
```
<br>
####4) Zapytanie o ilość edytowanych postów nie zwraca żadnej wartości, chodź powinno, gdyż wszystkich postów jest 53 851 542.
Różnica między wynikami dwóch zapytań powinna wynosić około 1 mln.

```javascript
db.RCOLL.aggregate ( [ {$match: {edited: true} }, 
                       {$group: { _id: null, count: 
                       {$sum: 1}}} ]
                   )
```


