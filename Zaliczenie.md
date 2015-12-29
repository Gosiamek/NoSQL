#EDA
## 1. Pobranie bazy danych
Postawnowiłam pobrać bazę danych reddita posiadającą dużą ilość komentarzy internautów ze strony: https://www.reddit.com/r/datasets/comments/3bxlg7/i_have_every_publicly_available_reddit_comment

#### Poniżej przedstawiam zużycie zasobów podczas pobierania bazy z sieci.

Zużycie procesora wyniosło około 30%
![](http://i.imgur.com/DQ7bMDI.jpg)

Pamięć RAM wykorzystana była również w około 30%
![](http://i.imgur.com/esB0gaa.jpg)

Dysk momentami osiągał nawet 100% aktywności
![](http://i.imgur.com/WzChCD5.jpg)

## 2. Import bazy danych do MongoDB

#### Fragmet kodu importujący bazę

```javascript
C:\Program Files\MongoDB\Server\3.0\bin>mongoimport --db reddit --collection RCOLL < C:\RC_2015-01\RC_2015-01.json
```

#### Zużycie zasobów komputera podczas importu

Obraz procesora w różnych momentach importu.<br>
Wykorzystanie procesora wahało się podczas importu od 30% do 50%.
![](http://i.imgur.com/ecXeJ9E.jpg)

Obraz pamięci w różnych momentach importu.<br>
Ilość wolnej pamięci w miarę upływu czasu potrzebnego na import malała.<br>
W połowie importu osiągnęła 97% i utrzymywała się na tym poziomie.
![](http://i.imgur.com/dX8l3f4.jpg)

Obraz dysku w różnych momentach importu.<br>
Praca dysku raz malała raz rosła osiągając 13, 30, a nawet 100%.
![](http://i.imgur.com/qYBWe4v.jpg)

Podgląd w monitorze zasobów w trakcie importowania
![](http://i.imgur.com/hO4vXgy.jpg)

#### Zliczenie rekordów w bazie oraz ogólne statystyki

```javascript
db.RCOLL.count()
db.RCOLL.stats()
```

![](http://i.imgur.com/pK5X4pk.jpg)

* Import do bazy danych przebiegł pomyślnie. Trwał 46 minut i 4 sekundy.<br> 
* Zaimportowano 53 miliony 851 tysięcy 542 rekordy.

## 3. Import bazy danych do PostgreSQL

#### Fragment kodu importujący bazę:

```javascript
C:\Program Files\PostgreSQL\9.4\bin>pgfutter_windows_amd64.exe --pw "arka1845" json "C:\RC_2015-01\RC_2015-01.json"
```
#### Zużycie zasobów komputeraz podczas importu

Wydajność procesora podcza importu bazy. Jego zużycie nie przekraczało 20%
![](http://i.imgur.com/gT3ZUxD.jpg)

Wykorzystanie pamięci osiągało niecałe 80%
![](http://i.imgur.com/07m4xoQ.jpg)

Praca dysku praktycznie przez cały okres importowania wynosiła 100%
![](http://i.imgur.com/5fhiYrh.jpg)

Monitor zasobów podczas importu
![](http://i.imgur.com/jdBLyFo.jpg)

#### Zliczenie wierszy w bazie

```javascript
postgres=# select count(*) from import.rc_2015_01;
```

* Import do bazy danych PostgreSQL trwał 85 minut i 57 sekund.<br>
* Zaimportowano 53 miliony 851 tysięcy 542 wiersze, co obliczono specjalną funkcją.<br>
* Zliczanie wierszy trwało 19 minut i 50 sekund.

## 4. Porównanie importowania bazy do MongoDB oraz do PostgreSQL

|                         |    MongoDB    |    PostgreSQL    |
|-------------------------|---------------|------------------|
|Czas importu             |  46 min. 4 s. |  85 min. 57 s.   |                  
|Zużycie zasobów procesora|    30%/50%    |      20%         |
|Zużycie zasobów pamięci  |     97%       |      80%         |
|Zużycie zasobów dysku    | 13%/30%/100%  |     100%         |
|Liczba rekordów          |   53 851 542  |   53 851 542     |
|Czas zliczania rekordów  |      0 s      |  19 min. 50 s.   |


#GeoJSON

Stworzyłam mapkę Polski oraz województw GeoJSON z wykorzystaniem http://geojson.io. 
Na mapie zaznaczone są [Zamki w Polsce](https://github.com/Gosiamek/NoSQL/blob/master/zamki.geojson).<br>
Kolorem *czerwonym* oznaczone zostały Zamki Krzyżackie, kolorem *niebieskim* Zamki Książęce oraz Królewskie, <br>
notomiast *zielonym* Fortyfikacje zbudowane bądź wykorzystywane przez duchownych.

### Importowanie bazy GeoJSON'ów do MongoDB

```javascript
mongoimport --db geojson --collection zamki < C:\mapa-mongo.json;
```
* Przykladowy rekord z bazy:
```javascript
      {
      "type": "Feature",
      "properties": {
        "marker-color": "#ff0000",
        "marker-size": "medium",
        "marker-symbol": "",
        "miasto": "Puck",
        "zamek": "Fortyfikacja Krzyzacka"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [
          18.411026000976562,
          54.720059699618325
          ]
        }
      }
```
* Zaimportowano 71 rekordów do bazy.

## Zapytania do bazy danych

#### OBIEKT POINT 
Zapytanie dotyczy zamków, które znajdują się w odległości 100 km od miasta Gdyni.

Aby móc tworzyć zapytania do bazy, należy nadać rekordom poszczególne indeksy, do których później będziemy się odwoływać.
```javascript
db.zamki.createIndex({"geometry": "2dsphere"})
```
Poniżej w zapytaniu podane zostały koordynaty miasta Gdyni od którego będzie mierzona odległość w milach.
```javascript
 var Gdynia = { "type":"Point",   "coordinates": [ 18.538055419921875, 54.51231286413694}

db.zamki.find ( {geometry : {$geoWithin : { $centerSphere : [Gdynia, 100/3963.2 ] } } } )
```
[MAPA ZAMKÓW BLISKO GDYNI](https://github.com/Gosiamek/NoSQL/blob/master/zamki_near_gdynia.geojson)

#### OBIEKT POLYGON
Zapytanie dotyczy zamków, które znajdują się na terenie różnych województw.<br>
Województwa zaznaczone zostały również za pomocą http://geojson.io

```javascript
db.zamki.find({geometry: {$geoWithin: {$geometry: {type: "Polygon",
coordinates: [
[
            [18.3306884765625, 54.83233630197034],
            [17.946166992187496, 54.82917227452137],
            [17.4957275390625, 54.7595009150459],
            .
            .
            .
            ] ]
      } } } }
 )
```
[MAPA WOJ. POMORSKIE](https://github.com/Gosiamek/NoSQL/blob/master/zamki_polygon_pomorskie.geojson)<br>
[MAPA WOJ. MAŁOPOLSKIE](https://github.com/Gosiamek/NoSQL/blob/master/zamki_polygon_malopolskie.geojson)

#### OBIEKT LINESTRING
Zapytanie dotyczy możliwości zwiedzenia zamków na trasie od Rumi do Zakopanego.<br>
Na tej trasie nie znaleziono żadnych zamków.

```javascript
db.zamki.find({geometry: {$geoIntersects: {$geometry: {type: "LineString", coordinates: [
          [18.389739990234375, 54.56947435457426],
          [18.4735107421875, 54.53781638269269],
          [18.489990234374996, 54.44928282456209]
          .
          .
          .
          ] ]
    } } } }
)
```
[MAPA TRASY Z RUMI DO ZAKOPANEGO](https://github.com/Gosiamek/NoSQL/blob/master/zamki_linestring.geojson)
