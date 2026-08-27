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

## Key DAX Measures

The project uses DAX for KPI calculation, time intelligence, dynamic rankings, HTML-based narrative insights, conditional formatting, and pickup versus drop-off location analysis.

### 1. Dynamic peak-demand narrative

This measure identifies the weekday with the highest trip volume, evaluates rolling four-hour pickup windows from 00:00 to 23:00, and returns an HTML insight describing the busiest day, time period, trip count, and share of that day’s trips.

```DAX
Trips Number Summary HTML =
VAR BestWeekDay =
    MAXX(
        TOPN(
            1,
            ADDCOLUMNS(
                VALUES('Dim Date'[FullDayName]),
                "TripCount", [Trip Num for Martix (No Blanks)]
            ),
            [TripCount], DESC
        ),
        'Dim Date'[FullDayName]
    )

VAR TotalTripsOnBestDay =
    CALCULATE(
        [Trips Number],
        'Dim Date'[FullDayName] = BestWeekDay
    )

VAR Windows =
    ADDCOLUMNS(
        GENERATESERIES(0, 20, 1),
        "WindowSum",
        CALCULATE(
            [Trips Number],
            FILTER(
                'Fact',
                HOUR('Fact'[lpep_pickup_time]) >= [Value] &&
                HOUR('Fact'[lpep_pickup_time]) <= [Value] + 3
            ),
            'Dim Date'[FullDayName] = BestWeekDay
        )
    )

VAR BestWindow =
    TOPN(1, Windows, [WindowSum], DESC)

VAR BestStartHour = MINX(BestWindow, [Value])
VAR BestEndHour = BestStartHour + 3
VAR BestWindowTripCount = MINX(BestWindow, [WindowSum])

VAR PercentOfTotal =
    DIVIDE(BestWindowTripCount, TotalTripsOnBestDay, 0)

RETURN
    "<p style='font-family:""Segoe UI Semibold"", sans-serif; font-size:16px; color:black;'>
        The Biggest Number Of Trips
        <span style='color:#647CA1;'><strong>" &
            FORMAT(BestWindowTripCount, "#,0") &
        "</strong></span>
        (<span style='color:green;'>" &
            FORMAT(PercentOfTotal, "0%") &
        "</span>) Occurs on
        <span style='color:#647CA1;'><strong>" &
            BestWeekDay &
        "</strong></span> from
        <span style='color:green;'><strong>" &
            FORMAT(TIME(BestStartHour, 0, 0), "h AM/PM") & " to " &
            FORMAT(TIME(BestEndHour, 0, 0), "h AM/PM") &
        "</strong></span>
    </p>"
```

### 2. Top pickup locations with share of trips

This measure creates a virtual zone-level summary table, identifies the first- and second-ranked pickup zones, calculates each zone’s share of trips in the active filter context, and generates a formatted HTML insight card.

```DAX
Top 2 Pick Up Zones HTML with % =
VAR ZoneSummary =
    ADDCOLUMNS(
        VALUES(TaxiZones[Zone]),
        "PickupCount", CALCULATE([Trips Number])
    )

VAR Top1Table =
    TOPN(1, ZoneSummary, [PickupCount], DESC)

VAR Zone1 =
    MAXX(Top1Table, TaxiZones[Zone])

VAR Count1 =
    MAXX(Top1Table, [PickupCount])

VAR Top2Table =
    TOPN(
        1,
        FILTER(
            ZoneSummary,
            TaxiZones[Zone] <> Zone1
        ),
        [PickupCount], DESC
    )

VAR Zone2 =
    MAXX(Top2Table, TaxiZones[Zone])

VAR Count2 =
    MAXX(Top2Table, [PickupCount])

VAR TotalTrips =
    CALCULATE([Trips Number])

VAR Pct1 =
    DIVIDE(Count1, TotalTrips, 0)

VAR Pct2 =
    DIVIDE(Count2, TotalTrips, 0)

RETURN
    "<p style='font-family:""Segoe UI Semibold"", sans-serif; font-size:16px; color:black;'>
        The Most Popular 2 Pick-Up Locations are:
        <br/>
        <span style='color:#647CA1;'><strong>" & Zone1 & "</strong></span> " &
        FORMAT(Count1, "#,0") & " Trips (<span style='color:green;'>" &
        FORMAT(Pct1, "0%") & "</span>)
        and <span style='color:#647CA1;'><strong>" & Zone2 & "</strong></span> " &
        FORMAT(Count2, "#,0") & " Trips (<span style='color:green;'>" &
        FORMAT(Pct2, "0%") & "</span>)
    </p>"
```

### 3. Drop-off analysis using an inactive relationship

The data model uses a taxi-zone dimension for both pickup and drop-off locations. This measure activates the inactive relationship between `Fact[DOLocationID]` and `TaxiZones[LocationID]`, allowing drop-off demand to be analysed without duplicating the zone dimension.

```DAX
Trips Number Dropoff Location =
CALCULATE(
    [Trips Number],
    USERELATIONSHIP(
        'Fact'[DOLocationID],
        TaxiZones[LocationID]
    )
)
```

### 4. Top drop-off location with percentage

This measure dynamically ranks drop-off locations using the inactive drop-off relationship, then returns the leading destination, its trip count, and its percentage of total drop-off trips as a formatted HTML narrative.

```DAX
Top 1 Drop-Off Zone HTML with % =
VAR ZoneSummary =
    ADDCOLUMNS(
        VALUES(TaxiZones[Zone]),
        "DropoffCount",
            CALCULATE([Trips Number Dropoff Location])
    )

VAR TopTable =
    TOPN(1, ZoneSummary, [DropoffCount], DESC)

VAR Zone1 =
    MAXX(TopTable, TaxiZones[Zone])

VAR Count1 =
    MAXX(TopTable, [DropoffCount])

VAR TotalDrops =
    CALCULATE([Trips Number Dropoff Location])

VAR Pct1 =
    DIVIDE(Count1, TotalDrops, 0)

RETURN
    "<p style='font-family:""Segoe UI Semibold"", sans-serif; font-size:16px; color:black;'>
        The Most Popular Drop-Off Location is:
        <span style='color:#647CA1;'><strong>" & Zone1 & "</strong></span> " &
        FORMAT(Count1, "#,0") & " Trips (<span style='color:green;'>" &
        FORMAT(Pct1, "0%") & "</span>)
    </p>"
```

### 5. Previous-month trip-distance

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
