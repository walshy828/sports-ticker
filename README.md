<!-- support_badges_start -->
[![PayPal](https://img.shields.io/badge/PayPal-Support%20Me-00457C?style=for-the-badge&logo=paypal&logoColor=white)](https://www.paypal.com/paypalme/KevinHughesPhoto)
<!-- support_badges_end -->

<div align="center">

# 🏟️ Home Assistant Sports Ticker

**ESPN-style scoreboards, tickers, standings, and stats — as Home Assistant sensors.**  
Build clean, fast, “TV ticker” cards in Lovelace with `button-card`, `card-mod`, and more.

</div>

---

## 📣 What’s new (v0.2.0)

- ⚽ **Soccer option added** (ESPN soccer scoreboards; great for MLS / EPL / LaLiga / UEFA comps)
- 🧾 **Standings + stats examples** added to the README (starter templates)
- 🧼 **README cleanup**: fixed rendering issues on GitHub (proper `<details>` summaries, consistent headings, no extra-indented code)

---

## 📌 Quick Links

- **Install:** HACS / Manual → [Installation](#-installation)
- **Setup:** Add integration + options → [Configuration](#️-configuration)
- **Entities:** What sensors you get → [Entities](#-entities--sensors)
- **Examples:** Copy/paste Lovelace cards → [Lovelace examples](#-lovelace-examples)
- **Help:** Common errors → [Troubleshooting](#-troubleshooting)

---

## ✨ What this integration does

- **Live scoreboard sensors** per league (JSON “raw” sensors + optional derived sensors)
- **Game day helpers** (what’s on tonight / next game)
- **Team-focused views** (favorite team filters, opponent, record, etc.)
- Works great with:
  - `custom:button-card`
  - `card-mod`
  - Mushroom cards / sections dashboards

> [!NOTE]
> If you’re using **Sections** view type, some cards may look “compressed.”
> Try setting `rows: 1.5` on the card’s `grid_options` if needed.
>
> ```yaml
> grid_options:
>   rows: 1.5
> ```

---

## ✅ Supported leagues

### 🇺🇸 Major leagues
- **MLB**
- **NFL**
- **NBA**
- **NHL**

### ⛳️ / 🏁 Other sports
- **PGA Tour**
- **NASCAR**

### ⚽ Soccer (Football)
Soccer is enabled as an option in the integration and uses ESPN soccer scoreboards.

Common ESPN soccer league slugs:
- **MLS** (`usa.1`)
- **Premier League** (`eng.1`)
- **LaLiga** (`esp.1`)
- **UEFA Champions League** (`uefa.champions`)
- **UEFA Europa League** (`uefa.europa`)

> Your build may support additional soccer leagues. If you know the ESPN slug, you can usually add it.

---

## 📦 Installation

### Option A — HACS (recommended)

1. Open **HACS → Integrations**
2. Click **⋮ → Custom repositories**
3. Add your repo URL, category **Integration**
4. Install **Sports Ticker**
5. Restart Home Assistant

### Option B — Manual

1. Copy the `custom_components/sports_ticker/` folder into:
   - `config/custom_components/sports_ticker/`
2. Restart Home Assistant
3. Add the integration via **Settings → Devices & services → Add integration**

---

## ⚙️ Configuration

After installing:

1. Go to **Settings → Devices & services**
2. Click **Add Integration**
3. Search for **Sports Ticker**
4. Choose options:
   - leagues you want enabled
   - poll interval
   - favorites (if supported)
   - ticker theme/speed (if supported)

---

## 🧠 Entities / Sensors

> Names vary slightly depending on your config flow options.
> The pattern below is what most users will see.

### Scoreboard “raw” sensors (JSON)

These are the best “source of truth” for Lovelace templates:

- `sensor.espn_mlb_scoreboard_raw`
- `sensor.espn_nfl_scoreboard_raw`
- `sensor.espn_nba_scoreboard_raw`
- `sensor.espn_nhl_scoreboard_raw`
- `sensor.espn_pga_scoreboard_raw`
- `sensor.espn_nascar_scoreboard_raw`
- `sensor.espn_soccer_scoreboard_raw` *(if soccer enabled)*

They typically expose `attributes.events` which contains:
- teams / competitors
- score
- status (pre / in / final)
- inning/period/time strings
- venue / broadcast (when available)

### Derived sensors (optional)

Depending on your version, you may also see:
- `sensor.sports_ticker_<league>_whats_on_tonight`
- `sensor.sports_ticker_<league>_next_game`
- `sensor.sports_ticker_<league>_standings_*`
- `sensor.sports_ticker_<league>_team_stats_*`

---

## 🧩 Lovelace examples

> Tip: Keep the README examples **short and clean**, and place full “big” cards in `/lovelace_examples/`.

### 1) ESPN-style ticker card (button-card)

![MLB ticker example](https://github.com/user-attachments/assets/5f63fdf8-9eaf-4400-a3b2-fd7f04b7ea17)

<details>
  <summary><strong>Show YAML</strong></summary>

```yaml
type: custom:button-card
show_name: false
show_state: false
variables:
  sport: MLB
  sensor: sensor.espn_mlb_scoreboard_raw
styles:
  card:
    - border-radius: 14px
    - overflow: hidden
    - padding: 0px
    - background: rgba(255,255,255,0.92)
    - border: 1px solid rgba(0,0,0,0.10)
    - box-shadow: 0 10px 22px rgba(0,0,0,0.18)
  custom_fields:
    ticker:
      - padding: 0px
custom_fields:
  ticker: |
    [[[
      const sport = (variables.sport || 'MLB').toUpperCase();
      const sensorId = variables.sensor || 'sensor.espn_mlb_scoreboard_raw';

      const raw = states[sensorId];
      const ev = raw?.attributes?.events || [];

      if (!ev.length) {
        return `<div style="padding:12px;opacity:.75">No games right now.</div>`;
      }

      // Example: show the first event only (you can map/scroll multiple)
      const g = ev[0];
      const comp = g?.competitions?.[0]?.competitors || [];
      const away = comp.find(x => x.homeAway === 'away') || {};
      const home = comp.find(x => x.homeAway === 'home') || {};

      const a = away?.team?.abbreviation || 'AWAY';
      const h = home?.team?.abbreviation || 'HOME';
      const as = away?.score ?? '--';
      const hs = home?.score ?? '--';

      const status = g?.status?.type?.shortDetail || g?.status?.type?.description || '';

      return `
        <div style="padding:12px;display:flex;justify-content:space-between;gap:12px;">
          <div style="display:flex;flex-direction:column;gap:6px;">
            <div><b>${a}</b> <span style="opacity:.8">${as}</span></div>
            <div><b>${h}</b> <span style="opacity:.8">${hs}</span></div>
          </div>
          <div style="text-align:right;opacity:.8;font-size:12px;line-height:1.2;">
            ${status}
          </div>
        </div>
      `;
    ]]]
```

</details>

---

### 2) Soccer scoreboard quick card (button-card)

<details>
  <summary><strong>Show YAML</strong></summary>

```yaml
type: custom:button-card
show_name: true
name: Soccer — Live
variables:
  src: sensor.espn_soccer_scoreboard_raw
styles:
  card:
    - padding: 14px
    - border-radius: 16px
custom_fields:
  body: |
    [[[
      const s = states[variables.src];
      const ev = s?.attributes?.events || [];
      if (!ev.length) return `<div style="opacity:.7">No matches found.</div>`;

      const g = ev[0];
      const comp = g?.competitions?.[0]?.competitors || [];
      const away = comp.find(x => x.homeAway === 'away') || {};
      const home = comp.find(x => x.homeAway === 'home') || {};

      const a = away?.team?.abbreviation || away?.team?.shortDisplayName || 'AWAY';
      const h = home?.team?.abbreviation || home?.team?.shortDisplayName || 'HOME';
      const as = away?.score ?? '--';
      const hs = home?.score ?? '--';
      const status = g?.status?.type?.shortDetail || '';

      return `
        <div style="display:flex;justify-content:space-between;gap:12px;">
          <div>
            <div><b>${a}</b> <span style="opacity:.8">${as}</span></div>
            <div><b>${h}</b> <span style="opacity:.8">${hs}</span></div>
          </div>
          <div style="text-align:right;opacity:.75;font-size:12px;">${status}</div>
        </div>
      `;
    ]]]
```

</details>

---

### 3) Standings card starter (Markdown)

If your build exposes derived standings entities, you can render them easily with Markdown:

<details>
  <summary><strong>Show YAML</strong></summary>

```yaml
type: markdown
content: |
  ## MLB Standings (example)
  - {{ states('sensor.sports_ticker_mlb_standings_division_leader') }}
  - {{ states('sensor.sports_ticker_mlb_standings_wildcard_1') }}
  - {{ states('sensor.sports_ticker_mlb_standings_wildcard_2') }}
```

</details>

---

## 🛠️ Troubleshooting

### “No games found” but you know games exist
- Check the league is enabled in the integration options
- Confirm the sensor has updated recently
- Open the raw sensor in **Developer Tools → States** and verify `attributes.events` exists

### `ButtonCardJSTemplateError: Identifier 'html' has already been declared`
If you copy/paste multiple button-card templates, avoid re-declaring the same `const` names in the same scope.
Use unique variable names or inline returns.

### Preseason vs regular season
Some leagues expose season type in the payload. If your cards need preseason,
filter by `event.season.type` or `event.season.slug` where available.

---

## 🗺️ Roadmap

- ✅ Add Soccer option
- ⏳ Standings sensors polish + more league coverage
- ⏳ More stats sensors (team + game level) + richer examples
- ⏳ Built-in ticker card templates

---

## 🙌 Support

If you enjoy this integration and want to support development:

- PayPal: https://www.paypal.com/paypalme/KevinHughesPhoto

---

## 🧾 Credits

- Data powered by public sports endpoints used by ESPN-style scoreboards  
- Home Assistant community for the ecosystem & inspiration

---

## 📄 License

MIT (or your preferred license). Add a `LICENSE` file to your repo.
