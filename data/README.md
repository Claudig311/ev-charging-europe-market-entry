# Data notes

One entry per raw file in `data/raw/`. Raw files are never edited by hand: all cleaning happens in Power Query.

All Eurostat files were downloaded on 2026-11-03 as full datasets in SDMX-CSV 1.0 format, with labels (not codes).

## Common structure of Eurostat files

Long format: one row per combination of dimensions and period.

| Column | Meaning |
| --- | --- |
| `geo` | Country or aggregate (full name) |
| `TIME_PERIOD` | Year (or half-year for electricity prices) |
| `OBS_VALUE` | The value |
| `OBS_FLAG` | Quality flag: `p` provisional, `e` estimated, `b` break in series, `d` definition differs, `i` see metadata |
| `DATAFLOW`, `LAST UPDATE`, `CONF_STATUS` | Metadata, not used |

Missing data appears as missing rows, not as empty values.

---

## Files

### `road_eqr_carpda_linear-03-11-2026.csv` — new passenger car registrations

- **Source:** Eurostat, dataset `road_eqr_carpda`
- **What it measures:** new passenger cars registered each year, by type of motor energy
- **Unit:** number of cars
- **Countries:** 42 incl. EU27, Norway, Switzerland and UK
- **Years:** 2013–2025 (UK only up to 2024)
- **Columns used:** `mot_nrg`, `geo`, `TIME_PERIOD`, `OBS_VALUE`
- **Categories used in `mot_nrg`:** `Electricity` (BEV), `Plug-in hybrid petrol-electric`, `Plug-in hybrid diesel-electric`, `Total`
- **Use:** BEV growth (CAGR) and BEV share of new sales
- **Issues:** some labels in `mot_nrg` end in non-breaking spaces → trim in Power Query

### `road_eqs_carpda_linear-03-11-2026.csv` — passenger car fleet (stock)

- **Source:** Eurostat, dataset `road_eqs_carpda`
- **What it measures:** passenger cars in circulation at year end, by type of motor energy
- **Unit:** number of cars
- **Countries:** 42 incl. EU27, Norway, Switzerland and UK
- **Years:** 2013–2025 (UK, Iceland and Liechtenstein only up to 2024)
- **Columns used:** `mot_nrg`, `leg_form`, `geo`, `TIME_PERIOD`, `OBS_VALUE`
- **Filters:** same `mot_nrg` categories as above; `leg_form` = `Total`
- **Use:** BEVs per charging point and public kW per BEV
- **Issues:** same trailing spaces in `mot_nrg`; `leg_form` splits owners into legal and physical persons, keep only `Total`

### `tps00001_linear-03-11-2026.csv` — population

- **Source:** Eurostat, table `tps00001` (used instead of `demo_pjan`, which was 67 MB because it breaks population down by age and sex)
- **What it measures:** population on 1 January
- **Unit:** persons
- **Countries:** 51 incl. aggregates
- **Years:** 2015–2026
- **Use:** per-capita indicators (e.g. BEVs per 1,000 inhabitants)

### `nama_10_pc_linear-03-11-2026.csv` — GDP per capita

- **Source:** Eurostat, dataset `nama_10_pc`
- **What it measures:** main GDP aggregates per capita
- **Countries:** 45 incl. aggregates
- **Years:** 1975–2025
- **Filters:** `na_item` = `Gross domestic product at market prices`; `unit` = `Current prices, purchasing power standard (PPS, EU27 from 2020) per capita`
- **Use:** purchasing power in the scoring
- **Issues:** 12 units and 9 aggregates in one file; PPS chosen because it corrects for price levels between countries

### `nrg_pc_204_linear-03-11-2026.csv` — household electricity prices

- **Source:** Eurostat, dataset `nrg_pc_204`
- **What it measures:** electricity price for household consumers
- **Unit:** euro per kWh
- **Countries:** 43 incl. aggregates
- **Periods:** 1985-S1 to 2026-S1 (half-yearly)
- **Filters:** `nrg_cons` = `Consumption from 2 500 kWh to 4 999 kWh - band DC` (standard household); `tax` = `All taxes and levies included`; `currency` = `Euro`
- **Use:** margin potential in the scoring
- **Issues:** two values per year (S1 and S2) → use the annual average or the latest semester; bands marked "pre-2007" are an old methodology, exclude them

### EAFO — public recharging points (total, AC, DC)

*To fill in.* Snapshot at September 2026, not a time series.

### EAFO — alternative fuel market share

*To fill in.* January–August 2026 (some countries up to September); BEV shown separately.

### EAFO — AFIR fleet-based target

*To fill in.* Fleet as of Q1 2026, infrastructure as of May 2026.

---

## Cross-source issues

- **Country names:** Eurostat and EAFO both use full names, but some may differ (e.g. Czechia / Czech Republic, Türkiye / Turkey). A mapping table will be needed in Power Query.
- **Aggregates:** remove rows such as `European Union - 27 countries (from 2020)` and `Euro area…` before analysis.
- **Reference dates:** Eurostat data runs to 2025; EAFO data is 2026. Note this in every chart that mixes both.
