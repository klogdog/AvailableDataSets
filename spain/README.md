# Spain city open data catalog
Research date: 7 October 2026

## What is included
A catalog of 74 selected large municipalities and regional or autonomous-city capitals, with 34 distinct dataset families and 140 city-to-dataset relationships. This is an editorial coverage list, not a population ranking, a list of all municipalities, or an assertion that every major-city municipal portal was exhaustively searched.

The scope is municipal boundaries, not metropolitan areas. This matters for Barcelona and Madrid suburbs, for regional transport feeds, and when joining statistics to geographic boundaries.

Coverage:
* 23 cities have at least one identified city-specific dataset or catalog listing.
* 28 additional cities have a regional dataset that can be filtered to a municipality or locality.
* 23 cities have only the common INE municipality-level population baseline in this pass.

Regional coverage means the publisher covers that region, not that this research parsed and confirmed a nonempty record set for every municipality.

## Files
* spain_open_data_catalog.xlsx: human-readable workbook, with overview, cities, dataset metadata, access URLs, discovery sources and an ingestion guide.
* spain_cities.csv: one row per city, portal links, dataset IDs, coverage classification and limitations.
* spain_datasets.csv: one row per distinct dataset family, including formats, publisher, licensing, freshness, links and verification.
* spain_city_dataset_links.csv: normalized city-to-dataset join table. Use this rather than splitting the pipe-separated lists.
* spain_discovery_sources.csv: catalog/API discovery services. These are NOT counted as underlying datasets.
* spain_open_data_catalog.json: all tables and metadata in one file, retaining arrays as JSON arrays.
* README.md: this guide.

CSV files use UTF-8 with a BOM, commas as the delimiter, quoted fields where necessary and a single header row. Multi-value CSV fields use ' | '. Internal CITY-xxx identifiers are catalog keys, NOT official INE municipality codes. Dates describing the research are ISO 8601 strings. Blank URLs mean that no exact URL was established; they do not mean the publisher has no data.

## Verification and limitations
Publisher pages, catalog entries and documented formats were reviewed. Some resource content types were observed, but no underlying source dataset was downloaded and parsed end to end. Observed HTTP errors and untested/documentation-derived API URLs are flagged.

A source returning a CSV or ZIP content type is weaker evidence than successfully parsing and validating its contents. A documented API URL is not a successful live API test. A portal landing page is not a bulk-download endpoint.

The catalog contains 18 direct-download URLs and 5 data API URLs. These counts include flagged access problems and documentation-derived URLs, not only working or tested endpoints. 28 dataset entries advertise CSV somewhere in their resource family. Only 13 entries have an explicit identified license in this research (some lack a confirmed version); the others require review of custom terms, portal notices, missing terms or conflicting metadata.

Do not infer that every source has an unrestricted commercial-use license. Public accessibility and machine readability alone do not establish reuse permission. License labels are descriptive catalog findings, not legal clearance.

## Important exceptions
* Valencia gardens: advertised CSV returned 404. JSON/GIS alternatives are listed but were not confirmed working.
* Alcobendas metro stops: advertised CSV returned 403 in the research tool. This may be an access/tool restriction rather than a permanent failure.
* Euskadi sports facilities: advertised GeoJSON returned 404. XLSX and JSON alternatives are listed but were not validated.
* Córdoba crashes: no license was specified in the observed dataset metadata. The 2024 file covers only through June; the linked complete-year example is 2023.
* Pamplona: conflicting portal-level licensing notices and mixed historical series. Confirm the selected file's license and year.
* Barcelona transport facilities: 2026 publication date does not change the observed 2021 data reference year.
* Andalucía sports census: observed catalog modification date is 2021. Do not assume current completeness.
* Logroño measurements and Donostia addresses: descriptions and frequency metadata disagree. Inspect the observations themselves.
* Santander and Mallorca: GTFS represents scheduled service. It is not equivalent to GTFS-Realtime or live arrival predictions.
* INE: the shared national table is municipality-level population, not a replacement for missing local operational datasets. Its API URL is constructed from official documentation and was not parsed during research.

## Cities with national population baseline only
Vigo; A Coruña; Oviedo; Móstoles; Santa Cruz de Tenerife; Leganés; Getafe; Albacete; Alcorcón; San Cristóbal de La Laguna; Badajoz; Torrejón de Ardoz; Parla; Ourense; Telde; Lugo; Santiago de Compostela; Rivas-Vaciamadrid; Lorca; Toledo; Mérida; Ceuta; Melilla.

Some of these cities have official portal leads in the Cities table. 'Baseline only' means a suitable local or regional data export was not verified in this pass, not that no such data exists.

## How to use the catalog
For an initial shortlist, filter the Datasets sheet/CSV by priority=start_here, then check verification_status, license_status and update_notes. This priority means a useful starting point, not a promise of successful ingestion.

Join spain_cities.csv to spain_city_dataset_links.csv on city_id, then join spain_datasets.csv on dataset_id. A regional dataset is stored once and linked to several cities rather than duplicated as separate datasets.

Use direct_data_url for an exact observed file URL, api_url for an actual data API route, and landing_url for resource selection and rediscovery. Timestamped filenames and storage identifiers can change. Retain both the landing page and the observed distribution link.

For CKAN:
* package_search and package_show retrieve catalog metadata.
* datastore_search retrieves rows only when that resource has DataStore support.
* Inspect result.fields, then page with limit and offset.
* The AOC shared budget API is unfiltered at the catalog URL. The source page shows a Sant Cugat filter. Confirm field names and casing before filtering other cities.

For INE:
* DATOS_TABLA retrieves statistical series. nult=1 requests the latest observation per series.
* Discover the actual series dimensions and municipality filter values from INE metadata instead of guessing codes.
* Do not sum municipality observations together with province or national total series.
Official documentation: https://www.ine.es/dyngs/DAB/index.htm?cid=1102

For geospatial data:
* Read the declared coordinate reference system before using X/Y as longitude/latitude.
* Donostia addresses use EPSG:25830.
* Zaragoza's documented example defaults to UTM30; its examples show srsname=wgs84 for longitude/latitude.
* Málaga's linked container export is EPSG:4326.
* Keep administrative codes as strings to preserve leading zeros.

For time series and CSV:
* Check observation timestamps independently of catalog modification dates.
* Detect the source file's encoding, separator and decimal convention. The catalog CSV conventions do not imply the same conventions for source files.
* Retain quality/validity flags and source units.
* A single API response may be one page, not the entire dataset.

## Minimal catalog reader
```python
import csv
import json
from pathlib import Path

catalog = json.loads(Path("spain_open_data_catalog.json").read_text(encoding="utf-8"))
by_id = {row["dataset_id"]: row for row in catalog["datasets"]}

with open("spain_city_dataset_links.csv", encoding="utf-8-sig", newline="") as f:
    for link in csv.DictReader(f):
        if link["city"] == "Madrid":
            dataset = by_id[link["dataset_id"]]
            print(dataset["title"])
            print(dataset["direct_data_url"] or dataset["api_url"] or dataset["landing_url"])
            print(dataset["verification_status"], dataset["license_status"])
```

## Provenance and maintenance
Every dataset row has source_url and checked_on. Every city row has source_urls. Where national catalog metadata was used, the source field retains that catalog URL separately from the publisher's distribution URL.

No source datasets are bundled with this catalog. No credentials, paid-access data, or PDF-only source datasets are included. The workbook and catalog files describe how to locate and consume data; they are not copies of the underlying observations.

Recommended refresh checks: resolve the dataset landing page, rediscover the current resource, check the license, fetch a small sample or bounded response, validate schema and CRS, verify observation dates, then download remaining pages. Respect publisher rate limits.

## Repository packaging

The discovery-source CSV was restored from the existing JSON `discovery` table during repository organization on 2026-10-07. No new source research was performed for that export.
