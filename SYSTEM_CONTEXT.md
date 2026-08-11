# 🏠 HomeAsset — System Context
**Last Updated:** 2026-05-27
**Type:** Docker / Python FastAPI Web App

---

## Purpose
Self-hosted home inventory management system. Tracks physical assets with hierarchical locations, photos, documents, and custom fields. Runs on local network — no cloud dependency, no login required.

---

## Tech Stack
| Layer | Technology |
|---|---|
| Backend | Python 3.11, FastAPI, SQLAlchemy (ORM), Uvicorn |
| Database | SQLite (single file: `/data/homeasset.db`) |
| Frontend | Vanilla HTML / CSS / JavaScript (SPA, no framework) |
| Fonts | Google Fonts — Inter |
| Container | Docker (single container, ~200MB image) |

---

## Key Files
```
app/
├── main.py          # FastAPI app, routes, seed data
├── models.py        # SQLAlchemy ORM models
├── schemas.py       # Pydantic request/response schemas
├── database.py      # DB engine and session factory
└── routers/
    ├── items.py     # CRUD + image/doc upload + CSV export
    ├── locations.py # Recursive location tree
    ├── categories.py
    ├── tags.py
    └── search.py
```

---

## Deployment
- **Port:** `7745` (host) → `8000` (internal Uvicorn)
- **Data volume:** `./data:/data` — persists DB, photos, documents
- **Pi deploy path:** `/home/pi/homeasset/`
- **Compose file:** managed by `homeasset-compose.yml` in pi5-scripts

```bash
docker compose up -d       # start
docker compose up -d --build  # rebuild after code changes
```

---

## Notes
- First boot auto-seeds example locations, categories, and tags
- All data lives in `./data/` — backup by copying that folder
- Tags are space-separated on input; deleted tags auto-remove from all items
- CSV export includes full location path (`House > Garage > Shelf A`)
- Part of the Gladstone Hub & Spoke webhost stack
