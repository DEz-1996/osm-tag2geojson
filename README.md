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


## Майбутні покращення
- Додати WireMock для стабільних інтеграційних тестів.
- Оптимізувати побудову геометрій для складних відношень.
