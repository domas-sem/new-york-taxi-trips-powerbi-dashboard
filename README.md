# NYC Taxi Trips Analysis Dashboard

An interactive Power BI dashboard exploring New York City taxi trip activity, including trip volume, passenger demand, fare performance, tipping behaviour, trip distance, pickup and drop-off locations, borough distribution, and payment preferences.

![NYC Taxi Trips Analysis Dashboard](screenshots/NYC%20Taxi%20Dashboard%20-%20Overview.png)

## Project Overview

This project presents an interactive analysis of NYC taxi trip data in Power BI. It is designed to help users explore when trips occur, which districts generate the most pickup and drop-off demand, how fares and tips perform, and how trip volume is distributed across boroughs.

The dashboard includes Year and Month slicers, allowing users to filter the report and observe how key performance indicators and visual insights change over time.

## Dashboard Features

- KPI cards for total fare revenue, total trips, total passengers, average fare per trip, average tip amount, and average trip distance.
- Year and Month slicers for interactive filtering.
- Previous-month comparison values displayed beneath KPI metrics.
- Daily and hourly trip-demand analysis using a day-of-week chart, hourly bar chart, and heatmap matrix.
- Dynamic key-insight cards highlighting the busiest travel period, most popular pickup locations, and primary drop-off location.
- Geographic pickup and drop-off analysis by district.
- Borough-level trip distribution and preferred payment type analysis.
- Visual cross-filtering between charts, maps, table, and donut chart.

## Key Insights

- Friday has the highest trip volume, with 20,314 trips in the displayed overall view.
- The highest concentration of trips occurs between 2 PM and 5 PM, with 6,413 trips representing 32% of the selected demand pattern.
- Jamaica and Forest Hills are the two most popular pickup locations.
- JFK Airport is the dominant drop-off location, accounting for 120,367 trips, or 99% of the displayed total.
- Queens has the highest borough-level trip volume, with 56,139 trips and 46.26% of total trips.
- Credit card is the leading payment type across Queens, Manhattan, and Brooklyn.

## Tools and Technologies

- **Power BI Desktop** — dashboard development and interactive visualisation.
- **Power Query** — data loading, cleaning, transformation, and preparation.
- **DAX** — KPI measures, previous-month comparisons, dynamic insights, and calculated metrics.
- **CSV** — taxi trip data source.
- **Excel** — taxi data dictionary.
- **JSON** — NYC taxi zone geographic data used in map visuals.

## Data Files

| File | Purpose |
|---|---|
| `data/TaxiDataShort.csv` | NYC taxi trip dataset used for analysis |
| `data/taxi dictionary.xlsx` | Field definitions and data dictionary |
| `data/TaxiZones.json` | Taxi-zone geographic data |
| `data/TaxiZonesMap.json` | Taxi-zone map configuration/geographic data |

## Dashboard Interactivity

### Default dashboard view

![Dashboard overview](screenshots/NYC%20Taxi%20Dashboard%20-%20Overview.png)

### Filtering by Year and Month

Users can select a Year and Month to update KPI cards, insights, charts, maps, and borough analysis dynamically.

![Filtered dashboard view](screenshots/NYC%20Taxi%20Dashboard%20-%20Filtered%20View.png)

### Cross-filtering visuals

Selecting a value in one visual filters and highlights related information across the report, helping users investigate demand patterns and geographic distribution.

![Cross-filtering dashboard view](screenshots/NYC%20Taxi%20Dashboard%20-%20Cross%20Filtering.png)

## Learning Sources and Attribution

This project was completed as a guided learning exercise using the following Power BI tutorials:

1. [Power BI tutorial: NYC Taxi Trips dashboard](https://www.youtube.com/watch?v=Fvdx8rfiOcM)
2. [Power BI tutorial: data model, measures, and report logic](https://www.youtube.com/watch?v=4ltnl-RNDu4)

The tutorials informed the data preparation, model structure, DAX measures, and analytical logic. I refined the final dashboard’s visual layout, colour palette, card styling, chart presentation, insight placement, and portfolio documentation.

## Repository Structure

```text
new-york-taxi-trips-powerbi-dashboard/
├── New York Taxi Trips Power BI Dashboard.pbix
├── README.md
├── data/
│   ├── TaxiDataShort.csv
│   ├── taxi dictionary.xlsx
│   ├── TaxiZones.json
│   └── TaxiZonesMap.json
└── screenshots/
    ├── NYC Taxi Dashboard - Overview.png
    ├── NYC Taxi Dashboard - Filtered View.png
    └── NYC Taxi Dashboard - Cross Filtering.png
```

## Author

Created by [Domas](https://github.com/domas-sem) as part of a Power BI and data analytics portfolio.
