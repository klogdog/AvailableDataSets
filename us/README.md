# US city open data catalog

Research date recorded in the supplied catalog: **2026-10-07**.

## Coverage

This catalog indexes 60 cities and 98 selected dataset entries. Its metadata describes the selection as 50 largest US city-proper entries by 2025 population estimates, plus 10 selected regional hubs. That ranking was not independently checked during repository organization. The collection provides starting datasets rather than complete inventories of each portal.

Topics include service requests, construction and permits, public safety, land use, transportation, environmental measurements, property, business, and public finance. City, consolidated-county, and transit-service boundaries differ; inspect `geographic_coverage` and row notes before joining sources.

## Files

| File | Contents |
| --- | --- |
| `us_city_open_data_catalog.xlsx` | Browsable workbook with Overview, Datasets, Cities, and Access guide sheets. |
| `us_city_open_data_catalog.json` | Research metadata, 60 city records, and 98 dataset records. |
| `us_cities.csv` | City index, portals, platforms, provenance, and coverage counts; exported from the existing JSON `cities` table during organization. |
| `us_city_open_data_datasets.csv` | All 98 dataset entries and their access evidence. |
| `us_city_open_data_file_and_api_links.csv` | The 40 tier A/B entries with published or constructed routes. These routes remain untested against dataset payloads. |

CSV readers should use UTF-8 with a BOM (`utf-8-sig` in Python). JSON retains the original structured values. Dataset records use `dataset_id`; city records and dataset records can be associated using the pair `city` and `state`.

## Access tiers

| Tier | Entries | Meaning |
| --- | ---: | --- |
| A | 16 | Published file URL or resolved layer API; not payload-tested. |
| B | 24 | Standard export/API endpoint constructed from an identified dataset ID; not payload-tested. |
| C | 58 | Dataset page with resource/export/API selection; exact data endpoint not resolved. Any CKAN API URL is catalog metadata rather than dataset rows. |

Publisher listings and indexed metadata were reviewed in the original research. Dataset payloads were not downloaded or parsed. Every dataset row records `download_tested=No`. These US tiers differ from Canada's readiness tiers.

## Using the catalog

Begin with `landing_url`, `endpoint_provenance`, `access_method`, `format_evidence`, and `evidence`. Use `download_url` or `api_url` when present, while retaining the landing page for rediscovery. Read `auth_notes`, `license_notes`, `series_status`, and geographic notes before selecting a source.

For ArcGIS, resolve the service and layer, inspect query capabilities, and handle pagination. For CKAN, inspect catalog resources to obtain the actual distribution URL. Socrata export and API access can have different authentication requirements; use the row's recorded notes and current publisher documentation. A GTFS schedule feed describes scheduled service, not live vehicle positions or arrivals.

The research date is separate from the date of source observations. Public availability alone does not establish unrestricted reuse. Retain publisher provenance and check the selected resource's terms and attribution requirements.

## Repository packaging

The existing JSON, dataset CSVs, and workbook were preserved byte-for-byte during organization on 2026-10-07. `us_cities.csv` was exported from the existing JSON city records, and this guide was added. Publisher endpoints were not re-tested during packaging.

See the [repository overview](../README.md) for the Canada and Spain catalogs and collection-wide guidance.
