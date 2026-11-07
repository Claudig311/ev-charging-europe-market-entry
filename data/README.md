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

### `eafo_recharging_points_2026-09.csv` — public recharging points

- **Source:** [EAFO, country comparison](https://alternative-fuels-observatory.ec.europa.eu/transport-mode/road/eu27-uk-norway-iceland-switzerland-turkey-liechtenstein), chart "Recharging points (EVSE)", downloaded 2026-11-03
- **What it measures:** publicly accessible recharging points by AFIR power category
- **Unit:** number of recharging points
- **Reference date:** September 2026 (snapshot, not a time series)
- **Countries:** 33 (EU27, Iceland, Liechtenstein, Norway, Switzerland, Turkey, UK)
- **Format:** wide, one row per country (`Category`) and one column per power category:

| Column | Type |
| --- | --- |
| Slow AC, single-phase (P < 7.4 kW) | AC |
| Medium-speed AC, triple-phase (7.4–22 kW) | AC |
| Fast AC, triple-phase (P > 22 kW) | AC |
| Slow DC (P < 50 kW) | DC |
| Fast DC (50–150 kW) | DC |
| Level 1 ultra-fast DC (150–350 kW) | DC |
| Level 2 ultra-fast DC (P ≥ 350 kW) | DC |

- **Use:** total, AC and DC points per country; BEVs per point; share of fast charging
- **Issues:** 4 empty cells (no points in that category); needs unpivoting in Power Query

### `eafo_af_market_share_2026-ytd.csv` — alternative fuel market share

- **Source:** EAFO, country comparison, chart "AF market share of total registrations (M1&N1)", downloaded 2026-11-03
- **What it measures:** share of new registrations by fuel type
- **Unit:** % of all new passenger cars and vans
- **Reference period:** January–August 2026 (12 EU countries up to September)
- **Countries:** 33
- **Columns:** `Category` (country), `BEV`, `PHEV`, `H2`, `LPG`, `CNG`, `LNG`
- **Use:** latest BEV share (2026), which Eurostat does not have yet
- **Issues:** includes vans (N1), unlike Eurostat; many empty cells in H2, LPG, CNG and LNG (no or negligible registrations)

### `eafo_afir_target_2026-05.csv` — AFIR fleet-based target

- **Source:** [EAFO, target tracker](https://alternative-fuels-observatory.ec.europa.eu/transport-mode/road/european-union-eu27/target-tracker), chart "Total power output per AFIR fleet-based target", downloaded 2026-11-03
- **What it measures:** public charging power installed vs. the AFIR minimum (1.3 kW per BEV + 0.8 kW per PHEV in the fleet)
- **Unit:** kW
- **Reference date:** fleet Q1 2026, infrastructure May 2026
- **Countries:** EU27 only
- **Columns:** `Category` (country), `Target kW`, `Total power (kW) output`
- **Use:** installed power / target ratio as the supply-gap indicator
- **Issues:** no data for Norway, Switzerland, UK, Iceland, Liechtenstein or Turkey; for those, compute an equivalent from the fleet and charging points data

---

## Cross-source issues

- **Scope:** analysis limited to EU27. Norway, Switzerland and the UK are excluded because AFIR does not apply to them and their data is incomplete (no AFIR tracker data, missing GDP in PPS for Switzerland and the UK, electricity prices missing for Switzerland and only up to 2020 for the UK).
- **Country names:** EAFO uses `Czech Republic`; Eurostat uses `Czechia`. All other EU27 names match. Handled with the `tbl_countries` mapping table in Power Query.
- **Format:** Eurostat files are long (one row per value); EAFO files are wide (one column per category).
- **Aggregates:** rows such as `European Union - 27 countries (from 2020)` and `Euro area…` are removed by the inner join with `tbl_countries`.
- **Reference dates:** Eurostat data runs to 2025; EAFO data is 2026. Note this in every chart that mixes both.
- **Vehicle types:** EAFO market share includes vans (M1 + N1); Eurostat registrations are passenger cars only.
- **PHEV gaps:** many countries only report plug-in hybrids separately from 2020–2022 onwards (registrations) and up to 2022 (fleet). These are left as null, not zero. BEV and total series are complete for all EU27 countries 2016–2025. PHEV data is complete from 2023.
- ---

## Processed tables

Built in Power Query in `data/processed/data_prep.xlsx`. Country filter and name mapping come from the `tbl_countries` sheet (EU27).

### `country_year` — one row per country and year (27 countries × 2016–2025 = 270 rows)

| Column | Meaning | Unit | Source |
| --- | --- | --- | --- |
| `country` | Country name (Eurostat style) | — | — |
| `year` | Year | — | — |
| `bev_reg` | New battery electric cars registered | cars | road_eqr_carpda |
| `phev_reg` | New plug-in hybrid cars registered (petrol + diesel) | cars | road_eqr_carpda |
| `total_reg` | All new passenger cars registered | cars | road_eqr_carpda |
| `bev_fleet` | Battery electric cars in circulation at year end | cars | road_eqs_carpda |
| `phev_fleet` | Plug-in hybrid cars in circulation at year end | cars | road_eqs_carpda |
| `total_fleet` | All passenger cars in circulation at year end | cars | road_eqs_carpda |
| `population` | Population on 1 January | persons | tps00001 |
| `gdp_pps_pc` | GDP per capita, current prices | PPS | nama_10_pc |
| `elec_price_eur_kwh` | Household electricity price, band DC, all taxes, average of S1 and S2 | EUR/kWh | nrg_pc_204 |

**Validation:** column totals reconciled against the raw files.

### `country_snapshot` — one row per country (27 rows), 2026 data from EAFO

| Column | Meaning | Unit |
| --- | --- | --- |
| `country`, `eafo_name`, `iso2` | Country name (Eurostat / EAFO style) and ISO code | — |
| `ac_slow` … `dc_ultra_350` | Public recharging points by AFIR power category | points |
| `ac_points`, `dc_points` | Total AC and DC public recharging points | points |
| `hpc_points` | High power charging points (≥150 kW) | points |
| `total_points` | All public recharging points | points |
| `bev_share_2026`, `phev_share_2026` | Share of new registrations (cars + vans), Jan–Aug 2026 | fraction (0.2045 = 20.45%) |
| `afir_target_kw` | Minimum public charging power required by AFIR | kW |
| `installed_kw` | Public charging power installed | kW |
