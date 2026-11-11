# Where should an EV charging operator expand in Europe?

Market entry analysis for an EV charging operator in Europe: country screening, weighted scoring, market sizing and Power BI dashboard.

> **Status:** work in progress. Analysis complete (data, screening, scoring, sensitivity, market sizing). Power BI dashboard and executive summary deck are next.

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

**Robustness:** across nine weight and method scenarios, only four countries ever reach the top 3: Denmark (8 of 9 scenarios), the Netherlands (7), Portugal (6) and Ireland (6). Denmark and the Netherlands are robust; the third place is contested between Portugal and Ireland and will be decided by market sizing.

## Recommendation

**Enter the Netherlands and Denmark first, with Portugal as the third market.**

- **Netherlands:** the largest public fast-charging market of the finalists (€351M in 2030, base case), with a large HPC gap despite the densest AC network in the EU. Competition from established operators is the main risk.
- **Denmark:** the most robust candidate in the scoring (top 3 in 8 of 9 scenarios and75% of 1,000 random weight sets), with the fastest BEV growth. Smaller market (€188M in 2030) and the lowest fast-charging price of the finalists.
- **Portugal:** wins the tie-break with Ireland on market size (€126M vs €96M in 2030, base case, and larger in every scenario), with the largest HPC gap in the shortlist.
- **Ireland:** the high-risk, high-reward option. It ranks first most often in random weight simulations but is the smallest market and the most sensitive to assumptions.
  
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

### 5. Sensitivity analysis

The ranking was recalculated under six weight scenarios and three method scenarios:

| Scenario | What changes |
| --- | --- |
| Base | Weights 30 / 25 / 20 / 15 / 10 |
| Equal | 20% each criterion |
| Growth-led | 40% demand growth |
| Supply-gap-led | 40% HPC supply gap |
| Size-led | 40% market size |
| Purchasing-power-led | 30% purchasing power |
| No log | Market size on a linear scale |
| No cap | Ireland's GDP not capped |
| Neither | Linear size and uncapped GDP |

| Country | Times in top 3 (of 9) | Worst rank |
| --- | --- | --- |
| Denmark | 8 | 4 |
| Netherlands | 7 | 4 |
| Portugal | 6 | 5 |
| Ireland | 6 | 7 |
| All others | 0 | — |

**Findings**

- **Denmark is the most robust candidate:** it only drops to 4th when the HPC supply gap gets 40% of the weight.
- **The Netherlands depends on capping Ireland's GDP:** without the cap, Irish GDP compresses everyone else's purchasing power score and the Netherlands falls to 4th. The cap is justified (Irish GDP is inflated by multinational profits) but changes the result, so it is disclosed here.
- **Portugal and Ireland cannot be separated by the scoring.** The GDP cap works against both: without it, Portugal rises to 2nd and Ireland to 3rd (or 1st if size is also linear). Ireland is the riskier choice: it falls to 7th when market size is weighted heavily, while Portugal never falls below 5th.
- **Germany never reaches the top 3:** its best result is 5th, with 40% of the weight on market size.

**Monte Carlo (1,000 random weight sets, uniform Dirichlet)**

| Country | % of simulations in top 3 | % ranked first |
| --- | --- | --- |
| Denmark | 75% | 29% |
| Netherlands | 68% | 18% |
| Ireland | 65% | 35% |
| Portugal | 49% | 12% |
| Germany | 15% | 6% |
| Belgium | 13% | 0% |
| France | 12% | 1% |

Random weights confirm the same top group and break the Portugal–Ireland tie in favour of Ireland. Ireland ranks first most often but is the most volatile: it is the high-risk, high-reward option, while Denmark is the safe choice.

### 6. Market sizing (finalists)

Public DC charging market value, 2026–2030, in low, base and high scenarios.

**Method:** BEV fleet is projected from 2025 by adding new BEV registrations each year (total registrations held at 2025 levels × BEV share rising linearly to a 2030 target). Market value = average BEV fleet × km per year × kWh per km × public DC share of charging energy × DC price per kWh.

| Market 2030 (M€) | Low | Base | High |
| --- | --- | --- | --- |
| Netherlands | 213 | 351 | 511 |
| Denmark | 120 | 188 | 260 |
| Portugal | 72 | 126 | 192 |
| Ireland | 53 | 96 | 150 |

At a 5% target share, VoltaCharge's 2030 revenue (base case) would be €17.6M in the Netherlands, €9.4M in Denmark, €6.3M in Portugal and €4.8M in Ireland.

**Key assumptions**

| Assumption | Value | Source |
| --- | --- | --- |
| Distance per car per year | EU average 10,800 km; Ireland 17,000 km | [ODYSSEE-MURE](https://www.odyssee-mure.eu/publications/efficiency-by-sector/transport/distance-travelled-by-car.html) (2023) |
| BEV energy consumption | 19.1 kWh/100 km (real-world average) | [EV Database](https://ev-database.org/cheatsheet/energy-consumption-electric-car) |
| DC charging price (median, incl. VAT) | DK €0.51 · NL €0.70 · PT €0.68 · IE €0.71 per kWh | [Eleport Price Report](https://eleport.com/price-report/) (October 2026) |
| Public DC share of charging energy | 10% / 15% / 20% | Estimate based on [BCG](https://web-assets.bcg.com/18/4f/edb337e546b4b2897ef61efdcef5/bcg-what-electric-vehicle-owners-really-want-from-charging-networks-jan-2023.pdf) and [IEA Global EV Outlook 2026](https://www.iea.org/reports/global-ev-outlook-2026/electric-vehicle-charging-chap-6-and-10) |
| BEV share of new registrations in 2030 | DK 75/85/95% · NL 50/65/80% · PT 30/45/60% · IE 25/40/55% | Own assumption, anchored on the EU 2030 CO2 target for new cars ([status](https://observatory.clean-hydrogen.europa.eu/eu-policy/co2-emission-performance-standards-passenger-cars-and-light-commercial-vehicles)) |
| VoltaCharge target share | 5% of the market in 2030 | Own assumption |

**Findings**

- **Portugal's market is 28–36% larger than Ireland's in every scenario**, assuming both evolve along comparable scenarios: it has more than twice the BEV fleet and almost twice the annual car sales. Ireland's higher mileage only partly offsets this.
- **The Netherlands is the largest market, 1.8–2.0× Denmark.**
- **Attractiveness and size point in different directions:** Denmark leads the scoring but is a smaller market with the cheapest fast charging. The choice between the two depends on competitive intensity and entry cost, which are not modelled.
  
### 7. Next steps
- Power BI dashboard and executive summary deck.
- Possible extensions: country-specific target shares based on competition, entry cost (capex per HPC site) and payback.
- 
## Limitations

- **Different reference dates:** BEV fleet is end-2025 (Eurostat); charging points are September 2026 (EAFO). BEVs per point is slightly understated for every country, so comparisons remain valid.
- **Electricity price is not scored:** a higher price can mean both higher revenue and higher energy costs for the operator. It is kept as context only.
- **PHEV data gaps:** many countries only report plug-in hybrids separately from 2020–2022 onwards. The analysis relies on BEV data, which is complete.
- **Competition is not scored directly:** the AFIR gap and HPC gap are used as proxies for how under-served each market is.
- **Market sizing uses constant 2026 prices including VAT:** values reflect what drivers spend, not the operator's net revenue.
- **A uniform 5% target share** ignores differences in competition between countries; it likely overstates what a new entrant can win in the Netherlands.
- **BEV fleet projection** ignores scrappage (the fleet is young) and holds total car registrations at 2025 levels. Denmark, the Netherlands and Portugal use the EU average mileage.

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
