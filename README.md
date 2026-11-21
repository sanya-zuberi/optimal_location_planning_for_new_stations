# Optimal Location Planning for New Stations

Comparing regional demand, utilisation and customer reach with the supply of stations and chargers, to recommend which regions are the best candidates for new EV charging stations.

## The Question

Where should the next stations be built? This project measures demand and supply by region, finds where supply is thinnest relative to demand, scores each region as a candidate, tests whether the ranking depends on the weights chosen, and estimates what a new station would earn. The dataset has no coordinates or population data, so it recommends regions, not specific sites.

## Repo Structure

```
├── notebooks/        → full analysis notebook
├── output/           → CSV outputs and interactive map
├── dashboard/        → Power BI .pbix file + page screenshots
├── slides/           → presentation deck
└── documentation/    → project documentation and insight summary
```

## Data

The source is the EV charging dataset: `stations` (33 stations in 9 regions), `chargers` (199 chargers, 25,600 kW installed) and `sessions` (294,024 sessions from 8,000 customers, January 2022 – December 2024). The raw file is not included in this repo. It is hosted as a Kaggle Dataset: https://www.kaggle.com/datasets/sanyazuberi/ev-charging-raw-dataset.

None of the sheets has coordinates, addresses, postcodes or population figures. The only place information is the region name.

Output files (in `output/`):
- `regional_supply_demand.csv`: one row per region: stations, chargers, installed kW, 2024 sessions, energy, revenue and customers, plus load and utilisation measures
- `gap_analysis.csv`: each region's share of demand, stations, chargers and installed power, and the gaps between them
- `customer_reach.csv`: customers reached per station and per charger in 2024
- `location_scores.csv`: the three scoring criteria, their scaled values, the score, rank, evidence level and approximate region centre (latitude and longitude)
- `ranking_stability.csv`: how often each region ranks first and in the top three across 2,000 random weightings
- `regional_yearly_demand.csv`: sessions and sessions per station per day by region and year
- `regional_hourly_demand.csv`: share of each region's 2024 sessions by hour of day
- `new_station_economics.csv`: estimated daily and yearly revenue and energy for four station designs
- `region_map.html`: interactive map of the location scores (background tiles need internet)

## How Locations Were Assessed

- **Region level, 2024.** The 9 regions are compared over 2024, the first year all 33 stations are open, so every region is measured over the same period. Stations are treated as available from 1 January of their activation year.
- **Utilisation** is the share of a charger's available time that it is in use, with overlapping sessions on the same charger merged before busy time is counted.
- **Gap analysis.** Each region's share of 2024 network demand (sessions) is compared with its share of stations, chargers and installed power. The gap is the demand share minus the supply share, in percentage points. A positive gap means the region has less supply than its demand share.
- **Customer reach.** The data has no customer locations, and customers use many regions, so customer density is measured as the number of different customers who charged at a region's stations in 2024, per station.
- **Location score.** Each region is scored from 0 to 100 on three criteria, each scaled from 0 (weakest region) to 1 (strongest): the supply gap (weight 40%), energy delivered per kW of installed power per day (40%), and customer reach per station (20%). Customer reach gets less weight because it partly reflects how few stations a region has.
- **Robustness test.** The regions were re-ranked 2,000 times with random weights (each draw splits the weight randomly between the three criteria) to see whether the ranking depends on the weights.
- **New-station estimates.** Revenue per day for a new station is the typical sessions per day multiplied by the average revenue per session of its chargers. The method was checked against the actual 2024 revenue of all 33 stations.
- **Map.** Region positions are approximate centres typed in by hand, since the data holds no coordinates.

## Key Findings

- **The data supports regions, not sites.** There are no coordinates, addresses or population figures, so specific sites cannot be named.
- **Demand is almost identical at every station.** Each station earns about 13.9 sessions a day on average (standard deviation 0.14, range 13.5 to 14.2), whatever its region, size or opening year. A region's demand therefore follows the number of stations it has, and the ranking rests on supply-side differences.
- **Customers do not belong to a region.** Customers with more than 5 sessions use a median of 8 of the 9 regions, and 99% of them use at least 5.
- **Demand patterns are the same everywhere.** Every region peaks between 10:00 and 14:00, and no region differs from another by more than 0.85 percentage points in any hour.
- **Supply gaps are small.** London has the largest gap (15.3% of demand against 12.6% of chargers, +2.7 points), followed by Midlands (+2.0) and South West (+1.5). North West has the most supply relative to demand (-2.9) and Scotland is next (-2.0).
- **No region is short of capacity.** Utilisation is 4.4% to 7.1% in every region (5.4% across the network), and London is the highest.
- **Wales is unusual.** Its one station has 8 chargers, all 50 kW, so its sessions deliver 33 kWh (44 to 51 kWh elsewhere) and its station earns the least revenue per day (267, against 355 to 413 elsewhere).
- **The shortlist is London, South West and Wales.** They are in the top three in 73% to 83% of 2,000 random weightings. North East, South East, Scotland and North West never reach the top three. The order within the shortlist depends on the weights.
- **New stations need no ramp-up and do not take sessions from older ones.** The 16 stations opened in 2024 earned 13.5 to 14.1 sessions a day in every month from the first month. The 8 stations opened in 2022 earned 13.92, 13.83 and 13.81 sessions a day in 2022, 2023 and 2024.
- **Revenue depends on the charger mix, not the region.** A session earns 19.4 on a 50 kW charger, 28.8 on 150 kW and 44.4 on 300 kW.

## Regional Supply and Demand (2024)

| Region | Stations | Chargers | Installed kW | Sessions | Utilisation |
|---|---|---|---|---|---|
| North West | 7 | 48 | 6,400 | 35,544 | 4.6% |
| London | 5 | 25 | 2,950 | 25,524 | 7.1% |
| Midlands | 5 | 26 | 4,000 | 25,180 | 5.7% |
| South East | 4 | 26 | 3,000 | 20,055 | 5.3% |
| South West | 3 | 15 | 2,300 | 15,173 | 5.5% |
| North East | 3 | 17 | 2,300 | 15,344 | 5.6% |
| Scotland | 3 | 22 | 2,900 | 15,152 | 4.4% |
| Yorkshire & Humber | 2 | 12 | 1,350 | 10,232 | 5.5% |
| Wales | 1 | 8 | 400 | 5,048 | 5.7% |

## Location Scores

| Rank | Region | Score | Evidence |
|---|---|---|---|
| 1 | London | 73.6 | 5 stations |
| 2 | Wales | 73.4 | One station (low) |
| 3 | South West | 60.0 | 3 stations |
| 4 | Midlands | 55.3 | 5 stations |
| 5 | Yorkshire & Humber | 53.9 | Two stations |
| 6 | North East | 48.8 | 3 stations |
| 7 | South East | 29.1 | 4 stations |
| 8 | Scotland | 14.0 | 3 stations |
| 9 | North West | 2.7 | 7 stations |

London and Wales are effectively tied. In the random-weight test, Wales ranks in the top three in 83% of weightings (first in 68%), South West in 80% (2nd to 4th in 90% of them) and London in 73% (first in 32%). Yorkshire & Humber reaches the top three in 47% and Midlands in 17%.

## What a New Station Would Earn

At the typical 13.85 sessions a day, with the revenue in the dataset's price units:

| Station design | Revenue per day | Revenue per year |
|---|---|---|
| All 50 kW chargers | 268 | 97,900 |
| All 150 kW chargers | 399 | 145,500 |
| All 300 kW chargers | 614 | 224,300 |
| Network-average mix | 374 | 136,700 |

The estimate matches each station's actual 2024 revenue (correlation 1.00). Build and running costs are not in the data, and 300 kW chargers cost much more to install than 50 kW ones.

## Recommendations

1. **Shortlist London, South West and Wales.** London has the largest supply gap and the highest utilisation. South West has a positive gap and the steadiest ranking. Wales has the highest load per kW and customer reach, but its evidence is a single station.
2. **Build faster chargers where a region has only slow ones.** Wales's station earns 267 a day against an estimated 399 with 150 kW chargers.
3. **Do not expand in North West or Scotland.** They have more supply than their demand share and never reach the top three.
4. **Plan on typical performance.** About 13.85 sessions and 374 revenue a day for a typical mix. The data cannot say whether any region will do better than another.
5. **Treat new stations as coverage, not capacity,** and keep them small, since no region is short of capacity.
6. **Get site-level data before choosing sites:** coordinates, EV registrations or population, traffic flows, grid capacity, competitor chargers and build costs.
7. **Open in stages.** Start with one or two stations in the top regions and compare their first 6 to 12 months with the 13.85 sessions-a-day benchmark.

## Dashboard

Built in Power BI, four pages:
- **Regional Demand**: region slicer, KPI cards, heatmaps of sessions per year, sessions per station per day, and share of sessions by hour, and sessions by region
- **Gap Analysis**: demand vs charger vs power share, supply gaps, customers reached per station, and a regional supply and load table
- **Location Score**: KPI cards, scores by region, ranking stability, and the scoring table
- **New Station Economics**: sessions per station per day, estimated revenue per day by station design, and the estimates table

See `dashboard/` for the `.pbix` file and page screenshots.

## Reproducing This Project

1. Download the raw dataset from the Kaggle link above.
2. Run the notebook in `notebooks/` from top to bottom in a fresh session. It writes the CSV files, the interactive map and the `figures/` folder.
3. Open the `.pbix` in `dashboard/` and refresh the data sources if the CSV paths have changed.

## Tools Used

Python (pandas, NumPy, Matplotlib, Seaborn, folium) · Kaggle Notebooks · Power BI

## Limitations

- The dataset has no locations, so the analysis recommends regions and cannot name sites. Only the 9 regions with stations can be assessed, so areas with no station are invisible.
- Demand per station is the same everywhere, so the ranking rests on supply-side differences. The gap analysis shows relative supply, not unmet demand.
- The criteria weights are a judgement call. The shortlist holds in most weightings, but the order within it does not (London and Wales are effectively tied).
- Wales rests on one station, and customer reach per station is strongly shaped by the number of stations a region has (correlation -0.93).
- Scores use 2024 only, and map positions are approximate region centres typed in by hand.
- Revenue figures are in the dataset's price units and ignore build and running costs.
- The dataset appears simulated: every station earns the same demand whatever its region, size or opening year, and new stations reach it from their first month, so the data may not reflect how real demand varies between places.
- Available time assumes each station operates from 1 January of its activation year, because the installation dates conflict with session activity.
