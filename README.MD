
# Amazon Web Scraper 🚀

**Scalable, Dockerized price tracking system with full data pipeline — Python, MongoDB, Elasticsearch.**

---

## Why this project matters

* Demonstrates solving a **real-world data engineering challenge**: collecting product data under anti-bot restrictions.
* Showcases **end-to-end pipeline engineering**: scraper → database → search → REST API → optional UI.
* Highlights **scalability, reliability, and containerized deployment** — skills highly valued in Big Tech.

---

## Key Features

* **Automated product tracking:** add a URL or keyword, snapshots scheduled automatically.
* **Scalable & reliable:** handles 100+ products over long runs.
* **Persistent & searchable:** MongoDB Replica Set for durability, Elasticsearch for fast queries.
* **One-command deployment:** Docker Compose handles full stack.
* **Anti-bot techniques:** UA rotation, randomized delays, and targeted HTML parsing.

> ⚠️ Demo blocked on AWS due to IP restrictions, works locally via Docker.

---

## Tech Stack

* **Backend & UI:** Python, Flask
* **Scraper:** BeautifulSoup / requests with anti-bot mitigations
* **Storage & Search:** MongoDB Replica Set, Elasticsearch
* **Deployment:** Docker / Docker Compose

---

## Quickstart (local)

```bash
git clone https://github.com/Alexadnre/AmazonWebScraper
cd AmazonWebScraper
# configure .env (sample provided)
docker-compose up --build -d
# Open UI at http://localhost:5000
```

---

## How it works (2-min walkthrough)

1. Open UI → search by keyword or product URL.
2. Add product → snapshot stored in MongoDB, indexed in Elasticsearch.
3. Monitor product page → price history updates automatically as scheduler runs.

---

## Core API Endpoints

| Endpoint                | Method | Description                      |
| ----------------------- | ------ | -------------------------------- |
| `/scrape`               | POST   | Trigger scrape by keyword or URL |
| `/products`             | GET    | List products                    |
| `/history/<product_id>` | GET    | Get price history                |

---

## Tests

* Basic database and update tests with `pytest`.
* Can run with services up or inside a dev container.

---

## Technical Highlight

* **Reliable Amazon scraping under anti-bot rules**:

  * UA rotation, randomized delays, targeted HTML parsing.
  * Limitation: cloud IPs can be blocked; next step: proxy/IP rotation for long-term deployment.

---

## Maintainer

**Alexandre Videlaine** — GitHub: [@Alexadnre](https://github.com/Alexadnre)
