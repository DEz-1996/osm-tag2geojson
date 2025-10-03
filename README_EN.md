### 📄 `README_EN.md`
```markdown
# Overpass API POI Fetcher

This project is a small Java utility for fetching **Points of Interest (POI)** from the **Overpass API** (OpenStreetMap) and saving results in **GeoJSON** format.

---

## Features

- Fetch POIs within a polygon (`ST_DWithin` support on DB side).
- Flexible CLI:
  - Polygon input (GeoJSON file or inline coordinates).
  - Category filter (e.g., `amenity=restaurant`, `shop=supermarket`).
  - Output file selection.
- Output as **GeoJSON FeatureCollection**.
- Unit and integration tests (with mocks for stability).
- Fat-jar build for standalone usage.

---

## Requirements

- **Java 21**
- **Maven 3.9+**
- Internet access (for real Overpass API calls).

---

## Build

```bash
mvn clean package
The jar will be created in target/:

pgsql
Копіювати код
target/overpass-poi-fetcher-1.0-SNAPSHOT-jar-with-dependencies.jar
Usage
Example: fetch restaurants within a polygon
bash
Копіювати код
java -jar target/overpass-poi-fetcher-1.0-SNAPSHOT-jar-with-dependencies.jar \
  --polygon "30.5234 50.4501,30.5240 50.4505,30.5250 50.4499,30.5234 50.4501" \
  --category "amenity=restaurant" \
  --output pois.geojson
Arguments:

--polygon – Polygon in lon lat format (or GeoJSON file).

--category – POI filter (multiple allowed).

--output – Result file path.

Output Example
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
Testing
Unit tests
bash
Копіювати код
mvn test
Geometry parsing

Overpass query building

JSON serialization

Integration test
Real API call with mocked response for stability:

bash
Копіювати код
mvn -Dtest=OverpassIntegrationTest test
Dependencies
Jackson – JSON serialization/deserialization

JUnit 5 – testing framework

Maven Assembly Plugin – fat-jar packaging

Notes
Overpass API has rate limits.

For large polygons, split queries or reduce categories.
