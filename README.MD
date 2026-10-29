# Amazon price tracker

Track products or search themes on amazon.fr, keep a price history, and search the catalogue instantly — a small data pipeline with scraping, a document store, a search index and a web UI, each in its own container.

Built in early 2025 as the final project of a data-engineering course (ESIEE Paris).

![Home](interface/static/img/screen_home.png)

## Architecture

```
            ┌──────────────┐   tasks (theme or URL,     ┌───────────────┐
 browser ──►│ Flask UI     │──  refresh interval)   ──► │ MongoDB       │
            │ :5000        │◄── products, history ───── │ replica set   │
            └──────────────┘                            └───────┬───────┘
                   ▲                                            │ change stream
                   │ search / autocomplete                      ▼
            ┌──────┴───────┐                            ┌───────────────┐
            │ Elasticsearch│◄── mongo-tracker (sync) ───│               │
            │ + Kibana     │                            │ task-executer │──► amazon.fr
            └──────────────┘                            │ (scheduler)   │
                                                        └───────────────┘
```

- **`task-executer/`** — polling scheduler. Reads the tracking tasks from MongoDB, runs the ones whose refresh interval has elapsed, writes products and price points back.
- **`scrapping/`** — `requests` + BeautifulSoup scraper for search pages and product pages (price, rating, image, ASIN), with rotating user agents and random delays.
- **`mongo_to_elastic/`** — listens to the MongoDB **change stream** (hence the replica set) and mirrors every insert/update/delete into an Elasticsearch index, so search never touches the primary store.
- **`interface/`** — Flask app: add a product URL or a search theme to track, see the price history, full-text search with autocomplete backed by Elasticsearch.
- **`mongo_requests/`, `shared/`** — data-access helpers shared by the services.

## Run it

```bash
git clone https://github.com/avidelaine/AmazonWebScrapper.git
cd AmazonWebScrapper
docker compose up --build
```

| Service | URL |
|---|---|
| Web UI | http://localhost:5000 |
| Kibana | http://localhost:5601 |
| Elasticsearch | http://localhost:9200 |

Container names, ports and database names are in `.env` (no secrets in it).

## Routes

| Route | Method | Purpose |
|---|---|---|
| `/` | GET | Home |
| `/track` | GET, POST | List tracked items; add a URL or a theme with a refresh delay |
| `/delete_product` | POST | Stop tracking an item |
| `/search` | GET | Search page |
| `/autocomplete` | GET | Suggestions from Elasticsearch |
| `/product/<id>` | GET | Product detail with price history |
| `/check_connections` | GET | Health of Mongo and Elasticsearch |

## Screens

| Tracking | History |
|---|---|
| ![Track](interface/static/img/screen_track.png) | ![History](interface/static/img/screen_history.png) |

## Limitations

Amazon changes its markup and rate-limits aggressively; the scraper is a course project, not a production crawler. There is no authentication on the UI. Tests under `test/` are ad-hoc connection scripts, not a test suite.
