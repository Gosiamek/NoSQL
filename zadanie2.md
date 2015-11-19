#1. Pobranie bazy danych
Postawnowiłam pobrać bazę danych reddita posiadającą dużą ilość komentarzy: https://www.reddit.com/r/datasets/comments/3bxlg7/i_have_every_publicly_available_reddit_comment
##Poniżej przedstawiam zużycie zasobów podczas ściągania.

####Zużycie procesora wyniosło około 30%
![](http://i.imgur.com/DQ7bMDI.jpg)

####Pamięć RAM wykorzystana była również w około 30%
![](http://i.imgur.com/esB0gaa.jpg)

####Dysk momentami osiągał nawet 100%
![](http://i.imgur.com/WzChCD5.jpg)

#2. Import bazy danych do MongoDB

##Fragmet kodu importujący bazę
```javascript
C:\Program Files\MongoDB\Server\3.0\bin>mongoimport --db reddit --collection RCOLL < C:\RC_2015-01\RC_2015-01.json
```

![](http://i.imgur.com/LvwmE7H.jpg)
![](http://i.imgur.com/i8MiRgH.jpg)

###Wykorzystanie procesora wahało się podczas importu od 30% do 50%
![](http://i.imgur.com/ecXeJ9E.jpg)

####Ilość wolnej pamięci w miarę upływu czasu potrzebnego na import malała.<br>
####W połowie importu osiągnęła 97% i utrzymywała się na tym poziomie.
![](http://i.imgur.com/dX8l3f4.jpg)

####Zużycie dysku raz malało raz rosło osiągając 13, 30, a nawet 100%.
![](http://i.imgur.com/qYBWe4v.jpg)

####Podgląd w monitorze zasobów
![](http://i.imgur.com/hO4vXgy.jpg)

####Zakończenie importu
![](http://i.imgur.com/x4tOphX.jpg)

####Zliczenie rekordów w bazie oraz ogólne statystyki

```javascript
db.RCOLL.count()
db.RCOLL.stats()
```

![](http://i.imgur.com/pK5X4pk.jpg)

####Import do bazy danych przebiegł pomyślnie. Trwał 46 minut i 4 sekundy.<br> 
####Zaimportowano 53 miliony 851 tysięcy 542 rekordy.

##Tak przedstawiało się wykorzystanie zasobów po zakończeniu importu.

####Procesor
![](http://i.imgur.com/7VSFySh.jpg)

#####Dysk
![](http://i.imgur.com/GujY0BB.jpg)