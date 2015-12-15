# EDA
# 1. Pobranie bazy danych
Postawnowiłam pobrać bazę danych reddita posiadającą dużą ilość komentarzy internautów ze strony: https://www.reddit.com/r/datasets/comments/3bxlg7/i_have_every_publicly_available_reddit_comment

## Poniżej przedstawiam zużycie zasobów podczas pobierania bazy z sieci.

#### Zużycie procesora wyniosło około 30%
![](http://i.imgur.com/DQ7bMDI.jpg)

#### Pamięć RAM wykorzystana była również w około 30%
![](http://i.imgur.com/esB0gaa.jpg)

#### Dysk momentami osiągał nawet 100% aktywności
![](http://i.imgur.com/WzChCD5.jpg)

# 2. Import bazy danych do MongoDB

## Fragmet kodu importujący bazę

```javascript
C:\Program Files\MongoDB\Server\3.0\bin>mongoimport --db reddit --collection RCOLL < C:\RC_2015-01\RC_2015-01.json
```

![](http://i.imgur.com/LvwmE7H.jpg)

### Wykorzystanie procesora wahało się podczas importu od 30% do 50%
![](http://i.imgur.com/ecXeJ9E.jpg)

#### Ilość wolnej pamięci w miarę upływu czasu potrzebnego na import malała.<br>
#### W połowie importu osiągnęła 97% i utrzymywała się na tym poziomie.
![](http://i.imgur.com/dX8l3f4.jpg)

#### Praca dysku raz malała raz rosła osiągając 13, 30, a nawet 100%.
![](http://i.imgur.com/qYBWe4v.jpg)

#### Podgląd w monitorze zasobów w trakcie importowania
![](http://i.imgur.com/hO4vXgy.jpg)

## Zliczenie rekordów w bazie oraz ogólne statystyki

```javascript
db.RCOLL.count()
db.RCOLL.stats()
```

![](http://i.imgur.com/pK5X4pk.jpg)

* Import do bazy danych przebiegł pomyślnie. Trwał 46 minut i 4 sekundy.<br> 
* Zaimportowano 53 miliony 851 tysięcy 542 rekordy.

# 3. Import bazy danych do PostgreSQL

## Fragment kodu importujący bazę:

```javascript
C:\Program Files\PostgreSQL\9.4\bin>pgfutter_windows_amd64.exe --pw "arka1845" json "C:\RC_2015-01\RC_2015-01.json"
```

![](http://i.imgur.com/WsawwH8.jpg)

#### Wydajność procesora podcza importu bazy. Jego zużycie nie przekraczało 20%
![](http://i.imgur.com/gT3ZUxD.jpg)

#### Wykorzystanie pamięci osiągało niecałe 80%
![](http://i.imgur.com/07m4xoQ.jpg)

#### Praca dysku praktycznie przez cały okres importowania wynosiła 100%
![](http://i.imgur.com/5fhiYrh.jpg)

#### Monitor zasobów
![](http://i.imgur.com/jdBLyFo.jpg)

#### Zakończenie importu
![](http://i.imgur.com/ddJCrPr.jpg)

## Zliczenie wierszy w bazie

```javascript
postgres=# select count(*) from import.rc_2015_01;
```

![](http://i.imgur.com/DNB8t2z.jpg)

#### Import do bazy danych PostgreSQL trwał 85 minut i 57 sekund.<br>
#### Zaimportowano 53 miliony 851 tysięcy 542 wiersze, co obliczono specjalną funkcją.<br>
#### Zliczanie wierszy trwało 19 minut i 50 sekund.

## Podgląd zaimportowanej bazy w programie pgAdmin, służącym do graficznego przeglądania zasobów bazy danych.
![](http://i.imgur.com/d5Ozu61.jpg)

#GeoJSON
