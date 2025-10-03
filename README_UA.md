# osm-tag2geojson
OSM → GeoJSON по довільних тегах key=value в межах заданого міста.  ✔ Підтримка: way + relation (type=multipolygon або type=boundary) ✔ Кілька --tag: об'єднання (UNION) вибірок ✔ Орієнтації кілець (RFC 7946): outer CCW, inner CW ✘ Експорт у WKT відсутній (за вимогою)
# OSM → GeoJSON Converter


Цей застосунок на Java 21 дозволяє завантажувати об’єкти з **OpenStreetMap** через **Overpass API** за довільними тегами `key=value` у межах заданого міста та зберігати їх у форматі **GeoJSON**.


## Функціональність


✔ Підтримка: `way` та `relation` (`multipolygon`, `boundary`)
✔ Можна передавати кілька `--tag` (логіка UNION)
✔ Коректна орієнтація кілець (RFC 7946): outer CCW, inner CW
✘ Експорт у WKT відсутній


## Приклади запуску


```bash
# Пошук парків у Києві
java -jar osm2geojson.jar --tag leisure=park --out parks.geojson


# Пошук торгових центрів у Києві
java -jar osm2geojson.jar --tag shop=mall --out malls.geojson


# Пошук декількох тегів одночасно
java -jar osm2geojson.jar --tag leisure=park --tag leisure=playground --out leisure.geojson
```


## Аргументи командного рядка


- `--endpoint` – адреса Overpass API (за замовчуванням: `https://overpass-api.de/api/interpreter`)
- `--city` – регулярний вираз для вибору міста (за замовчуванням: `^(Київ|Kyiv)$`)
- `--tag` – фільтр у форматі `key=value` (можна передавати кілька разів)
- `--out` – шлях до вихідного файлу GeoJSON (за замовчуванням: `output.geojson`)


## Структура вихідного GeoJSON


Результат — стандартний **FeatureCollection**, де:
- `geometry` — об’єкт типу `Polygon` або `MultiPolygon`
- `properties` — усі теги з OSM + службове поле `_osm_id`


Приклад:


```json
{
"type": "FeatureCollection",
"features": [
{
"type": "Feature",
"geometry": {
"type": "Polygon",
"coordinates": [[[30.5, 50.5], [30.6, 50.5], [30.6, 50.6], [30.5, 50.6], [30.5, 50.5]]]
},
"properties": {
"leisure": "park",
"_osm_id": "way:123456"
}
}
]
}
```


## Тести


Використано **JUnit 5**:
- Юніт-тести: перевірка геометричних утиліт, генерації запитів та експорту у GeoJSON.
- Інтеграційний тест:
- `testRealOverpassCall` — реальний виклик Overpass API (позначено `@Disabled`).
- `testMockOverpassCall` — мок-дані для стабільності.


## Залежності


- Java 21+
- Jackson (для роботи з JSON)
- JUnit 5 (для тестів)

///////////////////////////////////////////////////////////////////////////////

# Overpass API POI Fetcher

Цей проєкт — невелика Java-утиліта для отримання **точок інтересу (POI)** з **Overpass API** (OpenStreetMap) та збереження результатів у **GeoJSON** форматі.

---

## Можливості

- Отримання POI в межах полігону (з підтримкою `ST_DWithin` на стороні БД).
- Гнучкий CLI:
  - Вхід полігону (GeoJSON або inline-координати).
  - Фільтр категорій (наприклад, `amenity=restaurant`, `shop=supermarket`).
  - Вибір файлу для виводу.
- Результат у форматі **GeoJSON FeatureCollection**.
- Юніт та інтеграційні тести (з моками для стабільності).
- Збірка fat-jar для самостійного запуску.

---

## Вимоги

- **Java 21**
- **Maven 3.9+**
- Доступ до інтернету (для реальних запитів до Overpass API).

---

## Збірка

```bash
mvn clean package
Файл буде створено у target/:

pgsql
Копіювати код
target/overpass-poi-fetcher-1.0-SNAPSHOT-jar-with-dependencies.jar
Використання
Приклад: отримати ресторани в межах полігону
bash
Копіювати код
java -jar target/overpass-poi-fetcher-1.0-SNAPSHOT-jar-with-dependencies.jar \
  --polygon "30.5234 50.4501,30.5240 50.4505,30.5250 50.4499,30.5234 50.4501" \
  --category "amenity=restaurant" \
  --output pois.geojson
Аргументи:

--polygon – полігон у форматі lon lat (або шлях до GeoJSON файлу).

--category – фільтр POI (можна кілька).

--output – шлях до результату.

Приклад результату
json
Копіювати код
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [30.523, 50.450]
      },
      "properties": {
        "id": 123456,
        "name": "Restaurant Example",
        "amenity": "restaurant"
      }
    }
  ]
}
Тестування
Юніт-тести
bash
Копіювати код
mvn test
Парсинг геометрії

Побудова Overpass-запитів

JSON-серіалізація

Інтеграційний тест
Реальний виклик API (з моками для стабільності):

bash
Копіювати код
mvn -Dtest=OverpassIntegrationTest test
Залежності
Jackson – для JSON

JUnit 5 – для тестування

Maven Assembly Plugin – для створення fat-jar

Примітки
Overpass API має обмеження по кількості запитів.

Для великих полігонів краще розбивати запит на частини.


## Майбутні покращення
- Додати WireMock для стабільних інтеграційних тестів.
- Оптимізувати побудову геометрій для складних відношень.
