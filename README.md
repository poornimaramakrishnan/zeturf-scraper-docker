# zeturf-scraper 🐴

> Scrape, store, and explore French horse racing results from [ZeTurf.fr](https://www.zeturf.fr) — beautifully.

[![Docker](https://img.shields.io/badge/Docker-ready-blue?logo=docker)](https://github.com/poornimaramakrishnan/zeturf-scraper-docker)
[![Python](https://img.shields.io/badge/Python-3.9%2B-green?logo=python)](https://python.org)
[![DuckDB](https://img.shields.io/badge/DuckDB-analytics-yellow)](https://duckdb.org)

---

## What is this?

A **production-grade scraper** for ZeTurf.fr that:

- Scrapes **all race results** from 2016 to today — Arrivée, Cotes, Partants
- Stores everything in **DuckDB** (blazing-fast analytics, single file, zero config)
- Serves a **beautiful web dashboard** with filters, tabs, and Excel export
- Ships as a **single Docker image** — run it anywhere

---

## Quick Start

```bash
# Pull and run the dashboard (serves on http://localhost:8000)
docker run -p 8000:8000 -v $(pwd)/data:/data \
  ghcr.io/poornimaramakrishnan/zeturf-scraper serve --db /data/races.duckdb

# Scrape a date range (e.g. all of 2025)
docker run -v $(pwd)/data:/data \
  ghcr.io/poornimaramakrishnan/zeturf-scraper scrape \
  --from 2025-01-01 --to 2025-12-31 --db /data/races.duckdb
```

Or with **docker-compose**:

```bash
git clone https://github.com/poornimaramakrishnan/zeturf-scraper-docker
cd zeturf-scraper-docker
docker compose up dashboard
```

---

## 🎮 SQL Playground — Try It Live!

> **[▶ Launch SQL Playground](https://poornimaramakrishnan.github.io/zeturf-scraper-docker/playground.html)** — Query 32,000+ races directly in your browser!

A fully interactive SQL environment powered by **DuckDB-WASM** with **1 year of pre-loaded data** (Apr 2025 → Mar 2026):

- 🐴 **32,110 races** · 200,604 results · 408,047 odds · 193,884 starters
- 🏟️ **417 hippodromes** across France and international
- 📚 **25+ built-in queries** — jockey win rates, favourite analysis, betting payouts, speed rankings, draw bias, and more
- ⬇️ **Export to CSV** with one click
- 🧠 **Write your own SQL** — full DuckDB SQL syntax supported

No server required. No installation. Everything runs in your browser.

---

## Dashboard

The built-in web UI lets you:

| Feature | Details |
|---|---|
| **Filter by date range** | Any range from 2016 to today |
| **Filter by hippodrome** | All 200+ venues |
| **Browse tabs** | Arrivée · Cotes · Partants · Races |
| **Paginate** | 25 / 50 / 100 / 500 rows per page |
| **Export to Excel** | One click → `.xlsx` download |

---

## Database Schema

Four clean tables in a single `.duckdb` file:

```sql
races      -- race metadata (date, hippodrome, type, distance, prize, result)
arrivee    -- finish order (position, horse, jockey, time, gap)
cotes      -- odds & payouts (bet type, combination, payout)
partants   -- starters (horse, jockey, trainer, weight, draw, odds)
```

Query it directly:

```python
import duckdb
db = duckdb.connect("races.duckdb")
db.sql("SELECT hippodrome, COUNT(*) FROM races GROUP BY 1 ORDER BY 2 DESC").show()
```

---

## CLI Reference

```
Commands:
  scrape   Scrape races for a date range
  serve    Launch the web dashboard
  export   Export a table to CSV or XLSX
  stats    Show database statistics

Options for scrape:
  --from        Start date (YYYY-MM-DD)
  --to          End date   (YYYY-MM-DD)
  --db          DuckDB file path  [default: zeturf.duckdb]
  --concurrent  Parallel requests [default: 5]
```

---

## docker-compose.yml

```yaml
services:
  dashboard:
    image: ghcr.io/poornimaramakrishnan/zeturf-scraper:latest
    command: serve --db /data/races.duckdb --host 0.0.0.0
    ports:
      - "8000:8000"
    volumes:
      - ./data:/data

  scraper:
    image: ghcr.io/poornimaramakrishnan/zeturf-scraper:latest
    command: scrape --from 2016-01-01 --to 2026-12-31 --db /data/races.duckdb
    volumes:
      - ./data:/data
    profiles: [scrape]
```

---

## Data Coverage

- **Dates**: 2016-01-01 → present
- **Hippodromes**: 200+ venues (France + international)
- **Race types**: Attelé, Monté, Plat, Haies, Steeple-Chase, Cross-Country
- **Tables**: Arrivée, Cotes (all bet types), Partants

---

## Built by

**Poornima Ramakrishnan**

📧 [poornima2489@gmail.com](mailto:poornima2489@gmail.com)  
🐙 [github.com/poornimaramakrishnan](https://github.com/poornimaramakrishnan)

---

## License

MIT — free to use, modify, and distribute.
