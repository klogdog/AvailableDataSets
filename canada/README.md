# Canadian city open data catalogue

Research date: 2026-10-07

## What is included
This is a curated catalogue of 70 municipalities across all 13 provinces and territories.
It emphasizes major municipalities, provincial and territorial capitals, metropolitan-area municipalities,
and selected regional centres. It is not a claim to list every city, every city above a defined population
threshold, or every dataset from each portal.

There are 107 city/dataset entries, representing
103 distinct dataset pages. There are specific local, regional or
territorial entries for 56 municipalities. The remaining 14
have a source index and a shared national municipal CSV fallback, not a verified local operational export.
A regional dataset may require filtering and is not necessarily complete within a municipality.

## Files
- canada_city_open_data_catalog.xlsx: formatted workbook with overview, datasets, cities, national source,
  access guide and caveats.
- datasets.csv: all city/dataset entries and their verification tiers.
- cities.csv: complete municipality index, portals, coverage status and national fallback.
- documented_formats_and_endpoints.csv: tiers A, B and C only.
- export_verification_needed.csv: tier D leads only.
- national_fallback.csv: one Statistics Canada resource covering Canadian municipalities.
- catalogue.json: equivalent structured data, metadata and status definitions.
- README.md: this guide.

CSV files use UTF-8 with a BOM for reliable Excel display of French place names.
Python readers can use encoding="utf-8-sig". JSON is UTF-8.

## Verification levels
A: A sample JSON response was inspected and returned records. Full exports were not downloaded.
B: Publisher or government metadata explicitly names the file format or exposes a download link.
C: A specific dataset was located; an export/API route was constructed from platform conventions or
   an inspected service schema. The resulting payload was not fetched.
D: A specific dataset lead was located, often through a Statistics Canada municipal-source manifest.
   Current export availability still needs confirmation. Potential ArcGIS formats are explicitly
   labelled unconfirmed, not presented as verified CSV/GeoJSON downloads.

Current counts: {'D': 69, 'C': 20, 'A': 2, 'B': 16}

No complete raw dataset was downloaded or profiled. The date above is the research/check date, not the
date of the underlying observations. Some sites returned JavaScript shells, blocked automated access,
or could only be inspected through publisher metadata indexed by search.

## Important fields
catalogue_entry_id identifies a city/dataset coverage row.
dataset_id identifies a distinct dataset page and is shared where regional pages repeat across cities.
direct_download_url is a download route, when one was located or derived.
api_url may be a records endpoint OR a metadata resolver; consult access_method.
metadata_api_url is for discovering an ArcGIS service or CKAN resource, not necessarily for fetching data.
formats and format_basis distinguish published formats from platform-level possibilities.
source_url records the source used for discovery or verification.
geographic_scope and publisher distinguish municipal data from regional / territorial alternatives.
licence is a notice, not a legal clearance. Review the current licence on the selected resource.

## Practical consumption
CSV and JSON are the simplest tabular options. GeoJSON preserves geometry. Shapefile ZIPs and
geodatabases need a GIS-aware reader. GTFS Schedule ZIPs contain comma-separated .txt tables;
GTFS Realtime uses Protocol Buffers and is not a CSV export.

For CKAN, resolve the dataset with package_show, inspect result.resources and choose the actual
machine-readable resource URL. For ArcGIS, open item metadata, inspect the service URL and layer,
then examine supported query formats and pagination. Do not assume a single API response is complete.
Socrata and Opendatasoft routes in tier C are derived links and should be tested before scheduling.

For the national baseline, use Statistics Canada table 98-10-0002-01. The ZIP response was identified
as application/zip, but its contents were not unpacked. WDS can resolve the current bulk download.
The observations are 2016 and 2021 Census counts, not current population estimates.

## Reuse and coverage cautions
Whitehorse municipal terms restrict commercial redistribution without permission; the selected
Government of Yukon Census CSV has its own open licence. The Lévis transit page has conflicting
reuse language and is not included as a cleared dataset. Brampton uses CC BY 4.0 rather than a
generic municipal Open Government Licence. Transit operators can have separate developer terms.

Windsor's legacy catalogue is not maintained; the city index points to its new portal.
Delta also has a replacement portal. Historical source URLs and links can change.

The City Sources sheet records unresolved local exports for some cities. A public dashboard,
web map or PDF report was not counted as a machine-readable dataset. RDN parks are a regional
lead and do not imply a complete City of Nanaimo parks inventory.

## Minimal local catalogue example
```python
import csv
from pathlib import Path

with Path("datasets.csv").open(encoding="utf-8-sig", newline="") as f:
    rows = list(csv.DictReader(f))

# A/B explicitly document data formats. C adds useful but untested derived routes.
documented = [r for r in rows if r["readiness_tier"] in {"A", "B"}]
csv_sources = [r for r in documented if "CSV" in r["formats"]]
for r in csv_sources:
    print(r["city"], r["dataset_title"],
          r["direct_download_url"] or r["dataset_url"])
```

## Official platform references

CSV / XLSX: https://docs.python.org/3/library/csv.html

GeoJSON / SHP / GDB: https://developers.arcgis.com/rest/services-reference/enterprise/query-feature-service-layer/

Socrata: https://dev.socrata.com/docs/queries/

Opendatasoft / Huwise: https://help.opendatasoft.com/apis/ods-explore-v2/

CKAN: https://docs.ckan.org/en/latest/api/

ArcGIS Hub / REST: https://developers.arcgis.com/rest/services-reference/enterprise/query-feature-service-layer/

GTFS Schedule: https://www.rtl-longueuil.qc.ca/donnees-ouvertes

Red Deer API: https://data.reddeer.ca/buildingpermits

Statistics Canada WDS: https://www.statcan.gc.ca/en/developers/wds

## Repository packaging

The national-fallback CSV was restored from the existing JSON `national_fallback` table during repository organization on 2026-10-07. No new source research was performed for that export.
