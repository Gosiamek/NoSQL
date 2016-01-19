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
####Zapytanie o ilość najgorszych wpisów u trzech najgorszych autorów.

```javascript
db.RCOLL.aggregate([ { $group: { _id: "$author", score: {$sum: "$score" } } },
                     { $sort: {score: 1} }, { $limit: 3} ], 
                     {allowDiskUse: true}
                   )
```
<br>
Wynik zapytania.
```javascript
{ "_id" : "wutshappening", "score" : -8990 }
{ "_id" : "dwimback", "score" : -5241 }
{ "_id" : "_PM_ME_YOUR_PROLAPSE", "score" : -5043 }
```
<br>
####Zapytanie o ilość najbardziej kontrowersyjnych komentarzy.

```javascript
db.RCOLL.aggregate([{$group:{_id:"$author", controversiality: {$sum: "$controversiality" }}}, 
                    {$sort: {controversiality:-1}}, {$limit: 4} ], 
                    {allowDiskUse:true}
                  )
```
<br>
Wynik zapytania.

```javascript
{ "_id" : "-------------------x", "controversiality" : 0 }
{ "_id" : "---------------_-", "controversiality" : 0 }
{ "_id" : "---", "controversiality" : 0 }
{ "_id" : "------------------__", "controversiality" : 0 }
```
