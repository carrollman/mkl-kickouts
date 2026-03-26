# MKL Gaels — Kickout Analysis Dashboard

An interactive match analysis dashboard for MKL Gaels Ladies Gaelic Football Club, built for the 2025–2026 LGFA Kerry Intermediate season. The dashboard connects live to a Google Sheet and updates automatically every time it is opened.

---

## Live Dashboard

**[Open Dashboard](https://yourusername.github.io/mkl-kickouts)**

Works on desktop, tablet, and mobile. No login required.

---

## What It Does

Analyses every kickout from each match — both MKL's own restarts and opposition kickouts — and turns raw coded data into tactical insights for players and coaches. All numbers are calculated directly from the master sheet on every load, so adding new match data requires no changes to the dashboard itself.

---

## Three Views

### 📱 Player Mode
Designed to be read in under 30 seconds in a dressing room or before warm-up.

- Five headline KPIs at a glance
- Live decision tree — IF/THEN calls for in-game situations (e.g. under heavy press, go short to Z4)
- Three key messages from the most recent data

### 📋 Coach Mode
Full analytical view covering every dimension of kickout performance.

- KPI overview with benchmark targets and gap analysis
- Possession flow funnel — kickout taken through to positive outcome
- Win % by length — Short / Mid / Long with risk indicators
- Win rate under pressure — Heavy / Light / No press
- First action analysis — which first move leads to a score or foul
- Zone volume vs efficiency — dual chart showing frequency and success rate per zone with drill-down
- Scenario matrix — win % for every Pressure × Kickout Length combination
- Kickout possession type breakdown — Clean Catch / Contested / Breaking Ball by half
- Tactical call analysis — Flat Four / Quick / Overload / Long with win rates and sample confidence
- 1st vs 2nd half performance trend
- Opposition kickout analysis — press success by their length, zone turnover map
- Match trend lines across the season
- Action plan — six training priorities derived from the data
- WhatsApp card generator — shareable match summary cards for the team group chat

### 📖 Glossary
Plain-language explanations of every metric, chart, and tactical term used in the dashboard. Includes a live search box and a pitch diagram showing the zone layout. Designed for coaches and players who are new to kickout analysis.

---

## KPI Benchmarks (2026)

| KPI | Benchmark | What It Measures |
|---|---|---|
| Own KO Win % | ≥ 75% | Kickouts resulting in MKL possession |
| 2-Phase Retention | ≥ 70% | Possessed still held after 4+ actions |
| Possession to Shot % | ≥ 55% | Won kickouts that generated a shot attempt |
| Immediate Turnover % | < 10% | Won kickouts lost within 0–1 actions |
| Positive Attack % | ≥ 50% | Won kickouts resulting in score or foul won |
| Opp Turnover % | ≥ 40% | Opposition kickouts won by MKL |

---

## Data Fields

All data is entered manually into the Google Sheet after each match using a structured coding template. One row per kickout.

| Column | Description |
|---|---|
| Match ID | Unique identifier per match (e.g. 2026-CL-MKL-v-DrC) |
| Kickout Number per Team | Sequential number per team per match |
| Time | Time of kickout (mm:ss) |
| Half | 1st or 2nd |
| Team | MKL or Opp |
| Tactical Call | System called (Flat Four / Quick / Overload / Long) |
| Length | S (Short) / M (Mid) / L (Long) |
| Zone | Z1–Z9 grid reference |
| Receiver Position | Midfielder / Half Forward / Half Back / Full Back |
| Pressure on Receiver | Heavy / Light / blank |
| Turnover Cause – Execution | GK execution error if applicable |
| Possession Type | Clean Catch / No Contest / Contested Catch / Breaking Ball / Free (From Kickout) / Out for Sideline |
| Outcome (Primary) | Won / Lost |
| Possession Gained By | MKL or Opposition |
| First Action After Win | Carry / Handpass / Kickpass / Foul Won |
| Retention After Win | Controlled Possession (4+ actions) / Short Retention (2–3 actions) / Immediate Turnover (0–1 action) |
| Attack Length | Long Attack (6+ actions) / Short Attack (≤5 actions) / Immediate (Shot ≤2 actions) |
| Resulting Outcome | Score / Foul Won / Turnover / Wide / Foul Conceded / Shot Saved / Drop Shot |
| Turnover Phase | Attack Build-Up / Shot Attempt / Exit Pass |
| Turnover Cause – Finish | Specific cause of turnover if applicable |
| Resulting Action | Score / Turnover / Wide / Reset Possession |

### Zone Layout

```
◻ OPPOSITION GOAL ◻
Z7  |  Z8  |  Z9   ← Opposition half
Z4  |  Z5  |  Z6   ← Midfield
Z1  |  Z2  |  Z3   ← Own half (nearest GK)
◻ MKL GOAL (GK)  ◻
```

---

## How to Update After a Match

1. Enter the new match rows into the Google Sheet using the coding template above
2. Open the dashboard — data loads automatically on every visit
3. Switch between individual match tabs or the combined season view at the top

No file changes required. The dashboard reads directly from the sheet on every open and auto-refreshes every 5 minutes if the tab stays open.

---

## How to Update the Dashboard File

If the layout, charts, or features are changed, a new `index.html` will be provided. To deploy it:

1. Go to this GitHub repository
2. Click **Add file** → **Upload files**
3. Upload the new file named `index.html`
4. Click **Commit changes**

The live URL stays the same. Changes go live within 60 seconds.

---

## Technical Setup

| Component | Detail |
|---|---|
| Data source | Google Sheets (published via Google Apps Script relay) |
| Hosting | GitHub Pages |
| Charts | Chart.js 4.4.1 |
| Data fetch | Google Apps Script Web App (CORS-safe, works from any device) |
| Auto-refresh | Every 5 minutes while tab is open |
| Offline | Shows last-loaded data if connection lost |

### Google Apps Script Relay

The dashboard fetches data via a Google Apps Script web app deployed from the master sheet. This avoids browser CORS restrictions and means the dashboard works correctly when opened as a local file or from any device without additional setup.

The Apps Script code:

```javascript
function doGet() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Master Sheet');
  const data = sheet.getDataRange().getValues();
  const csv = data.map(row => row.map(cell => {
    const s = String(cell).replace(/"/g, '""');
    return s.includes(',') || s.includes('\n') || s.includes('"') ? `"${s}"` : s;
  }).join(',')).join('\n');
  return ContentService.createTextOutput(csv)
    .setMimeType(ContentService.MimeType.TEXT);
}
```

If the Apps Script URL ever needs to be replaced (e.g. after a new deployment), update the `SHEET_URL` constant at the top of the `<script>` block in `index.html`.

---

## Data Quality Notes

- **Tactical Call** must be one of the recognised values (Flat Four / Quick / Overload / Long) for the Tactical Call Analysis section to include it. Free-text notes in that column are ignored.
- **Sample size tags** (HIGH / MED / LOW) appear throughout the dashboard. Any metric with fewer than 3 events should be noted but not acted on until more data is available. The dashboard requires n ≥ 5 before generating a definitive tactical recommendation.
- The **CM match (2025-IF-MKL-v-CM)** currently has no Tactical Call data coded. The Tactical Call Analysis section will show an uncoded warning until those rows are filled in.
- Rows missing a valid Team (MKL / Opp) or Half (1st / 2nd) value are automatically skipped during parsing.

---

## Club

**MKL Gaels Ladies Gaelic Football**
Kerry Intermediate — LGFA
Season: 2025–2026# mkl-kickouts
