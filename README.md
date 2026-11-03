# Where should an EV charging operator expand in Europe?

Market entry analysis for an EV charging operator in Europe: country scoring, market sizing and Power BI dashboard.

> **Status:** work in progress. Findings and recommendation will be added once the analysis is complete.

## The client question

*VoltaCharge* (fictional) is a mid-sized public charging operator (CPO) based in Spain. It wants to expand abroad and needs to decide:

1. **Which 2–3 European countries should it enter first?**
2. **How large is the public charging market in each of them, 2026–2030?**

Scope: EU27 plus Norway, Switzerland and the UK; public charging for passenger cars, with a focus on DC fast charging.

**Hypothesis to test:** the most attractive markets are not the ones with the most electric cars today, but the ones where demand grows fast and charging supply lags behind.

## Recommendation

*Coming soon.*

## Key findings

*Coming soon.*

## Approach

1. **Data:** combine charging infrastructure and EV fleet data by country and year.
2. **Indicators:** BEVs per public charging point, public kW per BEV vs. the AFIR minimum of 1.3 kW, growth in BEV registrations, BEV share of new sales, share of DC charging.
3. **Country scoring:** weighted, normalised score across demand growth, supply gap, market size, margin potential and competition.
4. **Sensitivity:** test whether the top 3 holds when weights or adoption assumptions change.
5. **Market sizing:** top-down estimate of public charging revenue for the top 3 countries in low, base and high scenarios.

## Data sources

| Source | Used for |
| --- | --- |
| [European Alternative Fuels Observatory (EAFO)](https://alternative-fuels-observatory.ec.europa.eu/) | Public charging points (AC/DC), BEV market share, AFIR target attainment |
| Eurostat `road_eqr_carpda` | New passenger car registrations by type of motor energy |
| Eurostat `road_eqs_carpda` | Passenger car fleet (stock) by type of motor energy |
| Eurostat `demo_pjan`, `nama_10_pc`, `nrg_pc_204` | Population, GDP per capita, household electricity prices |

Details on each file are in [`data/README.md`](data/README.md).

## Repository structure

```
├── data/
│   ├── raw/          # original downloads, never edited
│   └── processed/    # clean country-year table
├── model/            # Excel scoring and market sizing model
├── dashboard/        # Power BI file and screenshots
└── deck/             # executive summary slides (PDF)
```

## Tools

Excel · Power Query · Power BI · DAX
