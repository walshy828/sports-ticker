<!-- support_badges_start -->
[![PayPal](https://img.shields.io/badge/PayPal-Support%20Me-00457C?logo=paypal&logoColor=white)](https://www.paypal.com/paypalme/KevinHughesPhoto)
<!-- support_badges_end -->

# 🏟️ Home Assistant Sports Ticker

> A Home Assistant integration that pulls live sports data from ESPN scoreboards and exposes it as sensors for Lovelace tickers, scoreboards, game cards, and dashboards.

---

## 📣 What's new (v0.0.16)

- 🧭 **New guided setup flow**
  - Step 1: Choose sports and leagues
  - Step 2: Choose favorite teams for each selected league

- ⭐ **Favorite team support**
  - Select a favorite team per league
  - Sensors expose:
    - `favorite_team`
    - `favorite_team_name`
    - `has_favorite_team`

- ⚽ **Expanded soccer support**
  - MLS
  - Premier League
  - LaLiga
  - Bundesliga
  - Serie A
  - Ligue 1
  - Champions League

- 💾 **Last-good-data caching**
  - Sensors keep showing the last valid ESPN data if the endpoint is temporarily unavailable
  - Adds cache/freshness attributes:
    - `stale`
    - `source`
    - `last_successful_update`
    - `last_attempted_update`
    - `last_error`

- 🧰 **Improved setup labels and translations**
  - Friendlier league names
  - Better configuration screens
  - Cleaner options flow

---

## ✨ What this integration does

- Creates live ESPN scoreboard sensors per selected league
- Exposes raw ESPN scoreboard data for Lovelace cards
- Lets you select favorite teams during setup
- Keeps the last good scoreboard data when ESPN is temporarily unavailable
- Adds cache/freshness attributes so dashboards can show whether data is live or cached
- Works great with:
  - `custom:button-card`
  - `card-mod`
  - Mushroom cards
  - Home Assistant sections dashboards

---

## 📌 Quick Links

| 📂 Category | 📝 Description | 🔗 Link |
| :--- | :--- | :---: |
| **🏠 Home** | This README | **You are here** |
| **⚙️ Installation** | HACS / manual setup | [Jump](#-installation) |
| **⚙️ Configuration** | Guided setup and favorite teams | [Jump](#️-configuration) |
| **🧠 Sensors** | What entities and attributes you get | [Jump](#-entities--sensors) |
| **🧩 Examples** | Lovelace usage notes | [Jump](#-lovelace-examples) |
| **🛠️ Troubleshooting** | Common issues | [Jump](#️-troubleshooting) |

---

## ✅ Supported leagues

Sports Ticker is designed around ESPN-style scoreboard endpoints.

### 🇺🇸 Major leagues

- **MLB**
- **NFL**
- **College Football**
- **NBA**
- **WNBA**
- **NHL**

### ⛳️ / 🏁 Other sports

- **PGA Tour**
- **NASCAR**

### ⚽ Soccer / Football

- **MLS**
- **Premier League**
- **LaLiga**
- **Bundesliga**
- **Serie A**
- **Ligue 1**
- **Champions League**

---

## 📦 Installation

### Option A — HACS recommended

1. Open **HACS** → **Integrations**
2. Click **⋮** → **Custom repositories**
3. Add this repository URL
4. Choose category **Integration**
5. Install **Sports Ticker**
6. Restart Home Assistant

### Option B — Manual

1. Copy the integration folder into Home Assistant:

```text
config/custom_components/sports_ticker/
```

2. Restart Home Assistant
3. Go to **Settings → Devices & services → Add integration**
4. Search for **Sports Ticker**

---

## ⚙️ Configuration

After installing:

1. Go to **Settings → Devices & services**
2. Click **Add Integration**
3. Search for **Sports Ticker**
4. Complete the guided setup

---

### Step 1 — Choose Sports & Leagues

Select the leagues you want Sports Ticker to create sensors for.

Examples:

- MLB
- NFL
- NBA
- NHL
- MLS
- Premier League
- College Football
- Champions League

You can also set:

- Poll interval
- Ticker speed
- Ticker theme

---

### Step 2 — Choose Favorite Teams

After selecting leagues, choose a favorite team for each selected league.

Examples:

- MLB → Atlanta Braves
- NFL → Atlanta Falcons
- MLS → Atlanta United FC
- Premier League → Manchester United

Favorite teams are exposed as sensor attributes so Lovelace cards can highlight favorite games, sort favorite matchups first, or build team-focused dashboards.

---

## 🧠 Entities / Sensors

Raw scoreboard sensors follow this pattern:

```text
sensor.espn_<league>_scoreboard_raw
```

Examples:

```text
sensor.espn_mlb_scoreboard_raw
sensor.espn_nfl_scoreboard_raw
sensor.espn_nba_scoreboard_raw
sensor.espn_nhl_scoreboard_raw
sensor.espn_mls_scoreboard_raw
sensor.espn_epl_scoreboard_raw
sensor.espn_laliga_scoreboard_raw
sensor.espn_bundesliga_scoreboard_raw
sensor.espn_seriea_scoreboard_raw
sensor.espn_ligue1_scoreboard_raw
sensor.espn_ucl_scoreboard_raw
```

---

### Main scoreboard attributes

Each raw scoreboard sensor exposes ESPN payload data such as:

```yaml
events:
  - ...
leagues:
day:
season:
next:
```

These attributes are the main source for Lovelace dashboard cards.

---

### Favorite team attributes

Each raw scoreboard sensor can expose favorite-team information:

```yaml
favorite_team: ATL
favorite_team_name: Atlanta Braves
has_favorite_team: true
```

For example:

```yaml
league: mlb
league_name: MLB
favorite_team: ATL
favorite_team_name: Atlanta Braves
has_favorite_team: true
```

These values come from the setup/options flow.

---

### Cache / freshness attributes

Sports Ticker now keeps the last good ESPN data when the endpoint is unavailable.

Attributes include:

```yaml
stale: false
source: espn
last_successful_update: "2026-05-05T23:00:00+00:00"
last_attempted_update: "2026-05-05T23:00:00+00:00"
last_error: null
```

If ESPN is temporarily unavailable, the sensor will stay available and show cached data:

```yaml
stale: true
source: cache
last_error: "Unexpected status 503"
```

This helps prevent Lovelace cards from going blank.

---

### Ticker helper attributes

The integration also exposes ticker display options as attributes for dashboard cards:

```yaml
ticker_speed: 12
ticker_theme: light
```

These are useful for `custom:button-card` templates.

---

## 🧩 Lovelace examples

Use the raw sensors as the source for custom dashboard cards.

Example sensor references:

```yaml
sensor.espn_mlb_scoreboard_raw
sensor.espn_nfl_scoreboard_raw
sensor.espn_mls_scoreboard_raw
sensor.espn_epl_scoreboard_raw
```

Favorite-team-aware cards can read:

```javascript
const favorite = entity.attributes.favorite_team;
const favoriteName = entity.attributes.favorite_team_name;
const hasFavorite = entity.attributes.has_favorite_team;
const stale = entity.attributes.stale;
```

Example cached-data indicator:

```javascript
const stale = entity.attributes.stale;
return stale ? 'Cached Data' : 'Live';
```

Example favorite-team check:

```javascript
const favorite = entity.attributes.favorite_team;
const events = entity.attributes.events || [];

const favoriteGames = events.filter(event => {
  const competitors = event.competitions?.[0]?.competitors || [];
  return competitors.some(team => team.team?.abbreviation === favorite);
});
```

---

## 🛠️ Troubleshooting

### I do not see the new setup screen

Restart Home Assistant after updating through HACS or manual install.

Then go to:

```text
Settings → Devices & services → Add integration → Sports Ticker
```

---

### I already had Sports Ticker installed

Open the integration options and run through the setup again:

```text
Settings → Devices & services → Sports Ticker → Configure
```

This lets you select favorite teams and update selected leagues.

---

### My sensor says cached

This means ESPN was unavailable, returned bad data, or timed out during the latest refresh.

The integration is keeping the last good data and setting:

```yaml
stale: true
source: cache
```

This is expected behavior.

---

### My favorite team does not show

Open the integration options again and choose a favorite team for that league.

```text
Settings → Devices & services → Sports Ticker → Configure
```

Then check the sensor attributes for:

```yaml
favorite_team:
favorite_team_name:
has_favorite_team:
```

---

### My dashboard card is blank

Check that the card is using the correct raw sensor.

Example:

```yaml
sensor.espn_mlb_scoreboard_raw
```

Also check whether the sensor has an `events` attribute.

---

## 📌 Version

Current version: **v0.0.16**

---

## ❤️ Support

If this project helps you build better Home Assistant dashboards, support is appreciated:

[![PayPal](https://img.shields.io/badge/PayPal-Support%20Me-00457C?logo=paypal&logoColor=white)](https://www.paypal.com/paypalme/KevinHughesPhoto)
