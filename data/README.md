# Data notes

One entry per raw file in `data/raw/`. Raw files are never edited by hand: all cleaning happens in Power Query.

**File naming:** `<source>_<dataset>_<download-date>.csv`, e.g. `eurostat_road_eqr_carpda_2026-11-03.csv`.

---

## Template (copy for each file)

### `<file name>`

- **Source:** <!-- organisation + link -->
- **Downloaded on:** <!-- YYYY-MM-DD -->
- **What it measures:** <!-- one sentence -->
- **Unit:** <!-- number, EUR, kWh, % ... -->
- **Countries covered:** <!-- e.g. EU27 + NO, CH -->
- **Years covered:** <!-- e.g. 2015–2025 -->
- **Columns used:**

| Column | Meaning | Notes |
| --- | --- | --- |
| | | |

- **Issues found:** <!-- missing values (Eurostat shows them as ":"), country names vs. codes, incomplete last year, revisions -->

---

## Files

### Eurostat `road_eqr_carpda` — new passenger car registrations by motor energy

*To fill in.*

### Eurostat `road_eqs_carpda` — passenger car fleet (stock) by motor energy

*To fill in.*

### Eurostat `demo_pjan` — population on 1 January

*To fill in.*

### Eurostat `nama_10_pc` — GDP per capita

*To fill in.*

### Eurostat `nrg_pc_204` — household electricity prices

*To fill in.*

### EAFO — public recharging points (total, AC, DC) by country

*To fill in.*

### EAFO — BEV market share by country

*To fill in.*

### EAFO — AFIR fleet-based target attainment

*To fill in.*

---

## Cross-source issues

- **Country identifiers:** Eurostat uses codes (`ES`, `DE`, `EL` for Greece); EAFO uses full names. A mapping table will be needed in Power Query.
- *Add more as you find them.*
