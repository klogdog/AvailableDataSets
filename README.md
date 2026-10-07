# Available Data Sets

Curated catalogs of municipal and regional open-data sources in **Canada, Spain, and the United States**. Each country folder contains an Excel workbook for browsing, CSV tables for filtering and joins, JSON for programmatic use, and a guide explaining coverage and access evidence.

**Catalog research date: 2026-10-07.** This is the recorded research date in the supplied catalogs, not the date of the underlying observations. Repository organization and consistency checks were completed on the same date; publisher endpoints were not re-tested during that work.

## Overall commentary

This collection is useful for discovering data, comparing municipal coverage, and choosing sources for a future ingestion pipeline. It retains publisher links, formats, geographic scope, access methods, provenance, and verification notes. Topics include permits, parks, public facilities, transportation, public safety, demographics, environmental measurements, property, and public finance.

Coverage and verification vary substantially. Canada has broad provincial and territorial coverage, with many specific dataset leads still awaiting export verification. Spain separates reusable dataset families from the municipalities they cover and explicitly distinguishes local, regional, and national-baseline coverage. The US catalog has a broad city index and selected starting datasets, with many endpoints requiring selection through the publisher portal.

These are **source catalogs**. The repository does not bundle full underlying dataset observations. A listed URL, advertised format, or regional coverage relationship does not establish a working download, a complete municipal extract, or current data. The collection is a practical starting point for source selection; ingestion requires checking the chosen resource and its actual records.

## Country catalogs

| Country | Municipalities / cities indexed | Catalog contents | Guide |
| --- | ---: | --- | --- |
| Canada | 70 across all 13 provinces and territories | 107 city/dataset entries representing 103 distinct dataset pages; one separate shared national fallback | [Canada guide](canada/README.md) |
| Spain | 74 selected municipalities | 34 distinct dataset families and 140 city-to-dataset relationships; seven discovery services | [Spain guide](spain/README.md) |
| United States | 60 cities | 98 selected dataset entries; 40 entries with published or constructed file/API routes | [US guide](us/README.md) |

The units differ by country, so these counts should not be added together as a total of unique underlying datasets. Discovery services and national fallbacks are also different from local operational datasets. The supplied US catalog describes its selection as 50 largest city-proper entries plus 10 regional hubs; the population ranking was not independently checked during repository organization.

## Folder layout

```text
AvailableDataSets/
├── README.md
├── .gitignore
├── .gitattributes
├── canada/
│   ├── README.md
│   ├── canada_city_open_data_catalog.xlsx
│   ├── catalogue.json
│   ├── cities.csv
│   ├── datasets.csv
│   ├── documented_formats_and_endpoints.csv
│   ├── export_verification_needed.csv
│   └── national_fallback.csv
├── spain/
│   ├── README.md
│   ├── spain_open_data_catalog.xlsx
│   ├── spain_open_data_catalog.json
│   ├── spain_cities.csv
│   ├── spain_datasets.csv
│   ├── spain_city_dataset_links.csv
│   └── spain_discovery_sources.csv
└── us/
    ├── README.md
    ├── us_city_open_data_catalog.xlsx
    ├── us_city_open_data_catalog.json
    ├── us_cities.csv
    ├── us_city_open_data_datasets.csv
    └── us_city_open_data_file_and_api_links.csv
```

Existing catalog filenames are retained within each country folder. Country-specific schemas and identifiers are preserved, so consumers should read each guide before combining tables.

## Choosing a starting point

- **Browse in Excel:** open a country's `.xlsx` workbook. Its overview and access-guide sheets explain the catalog.
- **Filter in code:** read the dataset CSV and its evidence fields. The CSV files use UTF-8 with a BOM; Python readers should use `encoding="utf-8-sig"`.
- **Join Spain's tables:** join `spain_cities.csv` to `spain_city_dataset_links.csv` using `city_id`, then to `spain_datasets.csv` using `dataset_id`. Internal city IDs are catalog keys, not official municipality codes.
- **Use structured metadata:** read the country JSON. It includes city tables and research metadata; Spain also includes relationships and discovery services, while Canada includes its separate national fallback.

Example, run from the repository root:

```python
import csv
from pathlib import Path

with Path("us/us_city_open_data_file_and_api_links.csv").open(
    encoding="utf-8-sig", newline=""
) as stream:
    for row in csv.DictReader(stream):
        print(row["city"], row["dataset_title"], row["access_tier"])
        print(row["download_url"] or row["api_url"] or row["landing_url"])
```

This example reads catalog metadata locally; it does not fetch or validate the linked data.

## Interpreting access evidence

The tier labels are country-specific. **Canada's A and the US's A do not mean the same thing.**

| Catalog | Evidence recorded |
| --- | --- |
| Canada | A: sample API records inspected (2 entries). B: formats/download links explicitly documented (16). C: endpoint derived but not payload-tested (20). D: specific lead requiring export verification (69). |
| Spain | Per-resource status strings distinguish documented APIs, observed file responses, listings, and access errors. Coverage includes 23 cities with city-specific entries, 28 with regional sources requiring filtering, and 23 with national population baseline only. |
| United States | A: published file URL or resolved layer API (16 entries). B: conventional route constructed from an identified dataset (24). C: resource selection still required (58). All 98 entries record `download_tested=No`. |

Canada's `documented_formats_and_endpoints.csv` includes tier C derived routes alongside A/B evidence. The US file/API shortlist likewise includes untested constructed routes. Neither shortlist is a list of confirmed working full exports.

## Provenance, reuse, and maintenance

Consult the source URL, verification status, observation period, geography, and publisher terms for each selected resource. A catalog modification date is separate from an observation date. Regional sources may require filtering, and municipal boundaries may differ from metropolitan, county, or transit-service boundaries. National population baselines do not fill gaps in local operational data.

Publisher licenses and attribution requirements remain attached to their individual resources. No blanket license is assigned here to the linked third-party datasets. Preserve each resource's provenance and terms when reusing it.

For a refresh, rediscover the resource from its landing page, resolve the current distribution or service layer, fetch a bounded sample, inspect the schema and coordinate system, check observation dates, and account for pagination before collecting the full dataset. Keep missing coverage explicit and record the date and outcome of each check.

During repository organization, all supplied CSV, JSON, and Excel data files were preserved byte-for-byte. Three companion CSVs were exported from existing JSON tables: Canada's national fallback, Spain's discovery sources, and the US city index. The country guides identify those additions. No new publisher observations were introduced by those exports.
