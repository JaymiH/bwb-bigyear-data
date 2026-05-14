# BWB Big Year Wizard — Data

Static JSON data files for the Big Year Bird Checklist Wizard at [betterwithbirds.com/pages/big-year-checklist](https://betterwithbirds.com/pages/big-year-checklist).

Served to the live wizard via **jsDelivr CDN**:

```
https://cdn.jsdelivr.net/gh/{owner}/bwb-bigyear-data@latest/{path}
```

Replace `{owner}` with the GitHub owner (org or user) and `{path}` with the file path below. jsDelivr picks up new commits within minutes.

## Layout

```
.
├── us-states/        50 US state files (e.g. oregon.json)
├── ca-provinces/     13 Canadian province/territory files
├── mx-states/        32 Mexican state files
├── countries/        185 country files (e.g. costa-rica.json)
├── continents/       6 continent files (e.g. north-america.json)
├── regions/          12 birding region files (e.g. pacific-northwest.json)
├── world.json        Single global species list (~11,500 species)
└── countries-manifest.json   Index of country slugs + names for the wizard's dropdown
```

## File schema

Every per-area JSON follows the same shape:

```json
{
  "area_type": "country",
  "area_code": "CR",
  "area_name": "Costa Rica",
  "generated_date": "2026-05-13",
  "total_species": 921,
  "data_sources": ["GBIF Occurrence Download API (CC0 + CC BY datasets)"],
  "seasonal_data_source": "GBIF country=US month distribution (fallback)",
  "species": [
    {
      "common_name": "Resplendent Quetzal",
      "scientific_name": "Pharomachrus mocinno",
      "family": "Trogonidae",
      "order": "Trogoniformes",
      "seasonal_status": "year-round",
      "likelihood": "common"
    }
  ]
}
```

Aggregated files (continents, regions, world) include `subarea_count` instead of native occurrence counts.

## Yearly refresh

Run the pipeline in the sister `big-year-wizard/` project, then push:

```bash
cd ../big-year-wizard/data-pipeline
python3 build_data.py --all-states --all-ca-provinces --all-mx-states
python3 build_data.py --download-api --parallel 3 --all-countries
python3 build_data.py --aggregate-regions --aggregate-continents --aggregate-world

# Copy refreshed outputs into this repo
./publish-to-data-repo.sh

cd ../../big-year-wizard-data
git add .
git commit -m "Yearly refresh — $(date +%Y)"
git push
```

jsDelivr auto-updates within ~10 minutes of push.

## Data sources + licensing

- **GBIF** — Occurrence records filtered to `CC0_1_0` and `CC_BY_4_0` licenses only. eBird-sourced records (`CC_BY_NC_4_0`) are excluded for clean commercial reuse.
- **GBIF Occurrence Download API** — Used for per-country species lists. Downloads are submitted with a registered GBIF account; each download is citable by DOI.
- **iNaturalist** — Cross-validation data is CC BY licensed.
- **USGS Breeding Bird Survey** — Public domain.

The aggregate data in this repo is released under **CC BY 4.0** with attribution to GBIF and contributing data providers (see `data_sources` field in each JSON).

## Attribution

Built and maintained by [Better With Birds](https://betterwithbirds.com). Powered by [GBIF](https://www.gbif.org/).
