---
tags: [übersicht]
erstellt: 2026-09-30
---

# Platform Engineering Assistant – Übersicht

Eine Flask-App mit PostgreSQL-Datenbank (in Docker), die Plattformen, Deployments, Incidents und KI-Agenten-Läufe verwaltet.

## Notizen

- [[Datenbank-Setup]]: Docker, `.env`, Migrationen, Admin anlegen, Datenbank ansehen
- [[extensions.py erklärt]]: warum `db` in einer eigenen Datei steht
- [[Datenmodell]]: alle Tabellen und ihre Beziehungen

## Projektaufbau

```
Platform_Engineering_Assistant/
├── app.py              # erstellt die Flask-App, verbindet db mit der App
├── extensions.py       # erzeugt db und migrate (ohne App)
├── models.py           # Tabellen-Baupläne
├── docker-compose.yml  # PostgreSQL-Container
├── .env                # Zugangsdaten (NICHT in Git!)
├── .env.example        # Vorlage für .env (in Git)
├── requirements.txt    # Python-Pakete
└── migrations/         # von Flask-Migrate erzeugt (in Git)
```

## Technik

| Teil | Werkzeug |
|---|---|
| Web-Framework | Flask 3.1 |
| ORM | Flask-SQLAlchemy / SQLAlchemy 2.1 |
| Migrationen | Flask-Migrate (Alembic) |
| Datenbank | PostgreSQL 17 + pgvector (Docker) |
| DB-Treiber | psycopg 3 |
| Konfiguration | python-dotenv (`.env`) |
