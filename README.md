# Where should an EV charging operator expand in Europe?

Market entry analysis for an EV charging operator in Europe: country screening, weighted scoring, market sizing and Power BI dashboard.

> **Status:** work in progress. Data preparation, indicators, screening and scoring are complete. Sensitivity analysis, market sizing and dashboard are next.

## The client question

*VoltaCharge* (fictional) is a mid-sized public charging operator (CPO) based in Spain. It wants to expand abroad and needs to decide:

1. **Which 2–3 European countries should it enter first?**
2. **How large is the public charging market in each of them, 2026–2030?**

**Scope:** EU27, public charging for passenger cars, with a focus on high power charging (HPC, ≥150 kW). Norway, Switzerland and the UK are excluded: the EU's charging regulation (AFIR) does not apply to them and their data is incomplete.

**Hypothesis to test:** the most attractive markets are not the ones with the most electric cars today, but the ones where demand grows fast and fast-charging supply lags behind.

## Preliminary results (base case)

| Rank | Country | Score (0–1) | Profile |
| --- | --- | --- | --- |
| 1 | Denmark | 0.678 | Balanced: fastest growth and high purchasing power, at least mid-range on every criterion |
| 2 | Netherlands | 0.637 | Large, wealthy market with a big HPC gap, despite the densest AC network in the EU |
| 3 | Portugal | 0.585 | Largest HPC gap and strong growth, held back by lower purchasing power |
| 4 | Ireland | 0.547 | Largest gaps in HPC and AFIR compliance, but the smallest market with slow growth |
| 5 | Belgium | 0.522 | Strong growth, but charging power already 3× the AFIR minimum |

*Results are preliminary until the sensitivity analysis confirms the ranking holds under different weights and method choices.*

**Early findings**

- **Charging network size is misleading.** The Netherlands has the densest network in the EU (2.8 BEVs per point), but only 3% of points are DC and there are 112 BEVs per HPC point, so a fast-charging gap remains.
- **Ireland is the only shortlisted country below the AFIR minimum** (0.94): a clear supply gap, but also a sign that grid connections or permitting may be slowing investment.
- **Germany is the largest market (2.0 million BEVs) but not the most attractive:** growth slowed to 5% a year after subsidies ended in December 2023, and its HPC coverage is already better than in most candidates. It ranks 7th.
- **High BEV sales share does not mean a saturated market.** BEVs are 68% of new car sales in Denmark, but only about 90 per 1,000 inhabitants: the fleet will keep electrifying for years.

## Approach

### 1. Data preparation
Eurostat and EAFO data cleaned and combined in Power Query into two tables: `country_year` (27 countries × 2016–2025) and `country_snapshot` (2026 charging infrastructure). Column totals were reconciled against the raw files. Details in [`data/README.md`](data/README.md).

### 2. Indicators
For each country: BEV fleet, BEV registrations CAGR 2022–2025, BEV share of new sales, BEVs per charging point, BEVs per HPC point, DC share of charging points, AFIR ratio, GDP per capita (PPS) and electricity price.

### 3. Screening: 27 → 11 countries

| Step | Rule | Countries left |
| --- | --- | --- |
| Start | EU27 | 27 |
| Home market | Exclude Spain (client already operates there) | 26 |
| Size | BEV fleet ≥ 100,000 | 12 |
| Growth | BEV registrations CAGR ≥ 10%, or BEV fleet ≥ 1 million (size exception) | 11 |

Shortlist: Austria, Belgium, Denmark, Finland, France, Germany, Ireland, Italy, Netherlands, Poland, Portugal. Germany enters through the size exception.

### 4. Scoring

| Criterion | Indicator | Weight | Treatment |
| --- | --- | --- | --- |
| Demand growth | BEV registrations CAGR 2022–2025 | 30% | Higher is better |
| HPC supply gap | BEVs per HPC point | 25% | Higher is better |
| Market size | BEV fleet | 20% | Log scale, so Germany does not flatten all other countries |
| Purchasing power | GDP per capita (PPS) | 15% | Ireland capped at the second-highest value: its GDP is inflated by multinational profits |
| AFIR gap | Installed power / AFIR minimum | 10% | Inverted: lower is better (more regulatory pressure to add capacity) |

Each indicator is normalised 0–1 across the 11 candidates (min-max) and combined with the weights. All thresholds and weights are editable cells in the model's `assumptions` sheet.

### 5. Next steps
- Sensitivity analysis: does the top 3 hold under different weights and method choices?
- Market sizing for the top 3, 2026–2030, in low, base and high scenarios.
- Power BI dashboard and executive summary deck.

## Limitations

- **Different reference dates:** BEV fleet is end-2025 (Eurostat); charging points are September 2026 (EAFO). BEVs per point is slightly understated for every country, so comparisons remain valid.
- **Electricity price is not scored:** a higher price can mean both higher revenue and higher energy costs for the operator. It is kept as context only.
- **PHEV data gaps:** many countries only report plug-in hybrids separately from 2020–2022 onwards. The analysis relies on BEV data, which is complete.
- **Competition is not scored directly:** the AFIR gap and HPC gap are used as proxies for how under-served each market is.

## Data sources

| Source | Used for |
| --- | --- |
| [European Alternative Fuels Observatory (EAFO)](https://alternative-fuels-observatory.ec.europa.eu/) | Public charging points by power category, BEV market share, AFIR target attainment |
| Eurostat `road_eqr_carpda` | New passenger car registrations by type of motor energy |
| Eurostat `road_eqs_carpda` | Passenger car fleet by type of motor energy |
| Eurostat `tps00001`, `nama_10_pc`, `nrg_pc_204` | Population, GDP per capita, household electricity prices |

## Repository structure

```
├── data/
│   ├── raw/                      # original downloads, never edited
│   ├── processed/
│   │   └── data_prep.xlsx        # Power Query: country_year and country_snapshot tables
│   └── README.md                 # data notes and data dictionary
├── model/
│   └── market_entry_model.xlsx   # assumptions, indicators, screening, scoring, notes
├── dashboard/                    # Power BI file and screenshots (coming soon)
└── deck/                         # executive summary slides (coming soon)
```

## Tools

Excel (Power Query, XLOOKUP, SUMIFS, RRI, dynamic arrays, conditional formatting) · Power BI (coming soon)
