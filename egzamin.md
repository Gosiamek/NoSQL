#Aggregation Pipeline

Do wykonania tego zadania użyje bazy danych redditów wykorzystana we wczesniejszym zadaniu.

## 1. Wywietlenie przykladowego rekordu.
<br>
Aby wyswietlić przykladowy rekord używamy nastepujacego zapytania do bazy.

```javascript
db.RCOLL.findOne()
```
<br>
Wyglad przykladowego rekordu możemy zobaczyć poniżej.
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

db.RCOLL.aggregate([ { $group: { _id: "$author", score: {$sum: "$score" } } },
{ $sort: {score: 1} }, { $limit: 3} ], {allowDiskUse: true})
