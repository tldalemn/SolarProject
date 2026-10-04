# Project Sunny St. Paul

**A solar field log.** How much of my home can a small solar battery bank power? I built one, tracked every charge, and did the math.

This repo holds a single-page site that tells the story of the project. It pulls live data from a Google Sheet, so the numbers update on their own as new charge sessions get logged.

## The Rig

| Component | Model | Key specs |
|---|---|---|
| Battery / power station | Grid Doctor 300 | 320 Wh LiFePO4, 2,000+ cycles to 80%, solar input 18 to 20 V, 4 A, 80 W max |
| Panel | Weize SP100W | 100 W monocrystalline, Vmp 18.78 V / Imp 5.32 A, Voc 22.64 V / Isc 5.70 A, 7.31 kg rigid glass |

The power station bundles the charge controller, inverter, and BMS into one sealed box. In a from-scratch DIY build, each of those would be a separate part to source, wire, and fuse.

## The Plan

1. Set the panel out whenever there's usable sun and let it feed the power station.
2. Log every solar charge: date, duration, and energy added.
3. Add it up in kWh at the end of the tracking period.
4. Apply Minnesota's grid emissions factor (about 0.4 kg CO₂ per kWh) to estimate what was avoided.

**Starting estimate:** 6 to 8 kWh generated over 3 months.

## Site Sections

| Tab | What it covers |
|---|---|
| 00 The Project | Intro and the core question |
| 01 The Rig | Equipment specs, photos, and the five parts of any solar setup |
| 02 The Plan | The tracking method |
| 03 The Ledger | Live charge log, running totals, and today/tomorrow solar outlook |
| 04 The Numbers | CO₂ avoided compared to a car commute, a rooftop array, and an average American's yearly footprint |
| 05 So What | Scaling up, community solar, green tariffs, small modular nuclear, and plug-in solar |

## How the Live Data Works

The page has no backend. It fetches two published Google Sheet CSVs in the browser and parses them with [PapaParse](https://www.papaparse.com/).

**Charging log** (`LOG_CSV_URL`)
Expects a header row starting with `Date` and these columns:

- `Date`
- `Duration (hrs)`
- `Energy Added (Wh)`
- `Avg Watts`
- `Running Total (kWh)`

Session rows end at a row labeled `Totals`. Below that, the script reads these summary labels from column A, with values in column B:

- `Total Energy Added (kWh):`
- `Total CO2 Avoided (kg):`
- `Total Sessions Logged:`

The CO₂ total also drives the "My panel, so far" bar on The Numbers tab.

**Solar forecast** (`FORECAST_CSV_URL`)
Expects a header row starting with `Date` and these columns:

- `Date` (YYYY-MM-DD)
- `Type` (`Today` or `Tomorrow`)
- `Summary`
- `Cloud Cover (%)`
- `Outlook`

Cloud cover sets the card color: 25% or less is good, over 60% is poor, anything between is middling.

The source workbook is `Grid_Doctor_300_Charging_Log.xlsx`, published to the web as CSV from Google Sheets.

## Repo Structure

```
.
├── index.html          # The whole site: markup, styles, and scripts
├── images/
│   ├── panel.jpg           # Multimeter reading the panel voltage
│   ├── cords.jpg           # Solar charging cable
│   ├── power-station.jpg   # Power station front panel
│   └── charging.jpg        # Power station charging under the panel
└── README.md
```

## Running It

No build step. Any of these work:

- Open `index.html` in a browser.
- Serve it locally: `python3 -m http.server` then visit `http://localhost:8000`.
- Host it on GitHub Pages: Settings → Pages → deploy from the `main` branch root.

The live ledger and forecast need an internet connection. If the sheets can't be reached, the page shows a fallback message instead of data.

## Using Your Own Data

1. Copy the charging log and forecast sheets into your own Google account.
2. In each sheet, go to File → Share → Publish to web, pick the right tab, and choose CSV.
3. Paste the new URLs into `LOG_CSV_URL` and `FORECAST_CSV_URL` in `index.html`.
4. Keep the column headers and summary labels exactly as listed above, or update the script to match.

## Built With

- Plain HTML, CSS, and JavaScript
- [PapaParse](https://www.papaparse.com/) for CSV parsing
- Google Fonts: Alfa Slab One and Arvo
- Google Sheets as a free, no-server data source

## Ideas for Later

- Measure actual panel output automatically (for example, an ESP32 with a current/voltage sensor) instead of logging by hand.
- Add a chart of energy added per session over time.

## Author

Tyler Dale
