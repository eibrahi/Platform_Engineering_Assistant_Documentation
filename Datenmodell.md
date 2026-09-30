---
tags: [datenbank, datenmodell]
erstellt: 2026-09-30
quelle: models.py
---

# Datenmodell

Zurück zur [[00 Übersicht]] · siehe auch [[Datenbank-Setup]]

## Beziehungen

```mermaid
erDiagram
    USERS ||--o{ CONVERSATIONS : "führt"
    USERS |o--o{ DEPLOYMENTS : "erstellt (created_by)"
    PLATFORMS ||--o{ DEPLOYMENTS : "hat"
    PLATFORMS ||--o{ INCIDENTS : "hat"
    PLATFORMS |o--o{ AGENT_RUNS : "betrifft"
    PLATFORMS |o--o{ CONVERSATIONS : "betrifft"
    CONVERSATIONS ||--o{ MESSAGES : "enthält"

    USERS {
        int id PK
        string name
        string email UK
        string role
        datetime created_at
    }
    PLATFORMS {
        int id PK
        string name UK
        string environment
        string cloud_provider
        string azure_subscription_id
        string azure_region
        string databricks_workspace_url
        string status
        datetime created_at
        datetime updated_at
    }
    DEPLOYMENTS {
        int id PK
        int platform_id FK
        string deployment_type
        string status
        string git_commit
        text terraform_plan
        datetime started_at
        datetime finished_at
        int created_by FK
    }
    INCIDENTS {
        int id PK
        int platform_id FK
        string title
        text description
        string severity
        string status
        text root_cause
        datetime created_at
        datetime resolved_at
    }
    DOCUMENTS {
        int id PK
        string title
        string source
        string document_type
        string version
        text content
        datetime created_at
    }
    AGENT_RUNS {
        int id PK
        int platform_id FK
        string agent_type
        jsonb input
        jsonb output
        string status
        datetime started_at
        datetime finished_at
    }
    CONVERSATIONS {
        int id PK
        int user_id FK
        int platform_id FK
        datetime created_at
    }
    MESSAGES {
        int id PK
        int conversation_id FK
        string role
        text content
        datetime created_at
    }
```

## Tabellen

| Tabelle | Zweck | Wichtig |
|---|---|---|
| `users` | Menschen, die die App nutzen | `email` ist eindeutig; es gibt noch kein Passwort-Feld |
| `platforms` | Azure-/Databricks-Umgebungen | `name` ist eindeutig; beim Löschen werden Deployments und Incidents mitgelöscht |
| `deployments` | Terraform-Deployments einer Plattform | `created_by` ist optional |
| `incidents` | Störungen einer Plattform | `root_cause` wird nach der Analyse ausgefüllt |
| `documents` | Wissensbasis (Runbooks, Doku) | später `embedding` mit pgvector |
| `agent_runs` | Läufe von KI-Agenten | `input`/`output` sind JSONB |
| `conversations` | Chat-Verläufe eines Users | optional an eine Plattform gebunden |
| `messages` | einzelne Chat-Nachrichten | beim Löschen einer Conversation werden sie mitgelöscht |

## Erlaubte Werte

Diese Werte sind in `models.py` als Konstanten festgelegt. Die Prüfung soll später in der API passieren.

| Feld | Werte | Standard |
|---|---|---|
| `users.role` | `admin`, `platform_engineer`, `viewer` | `viewer` |
| `platforms.environment` | `dev`, `test`, `prod` | – |
| `platforms.status` | `healthy`, `degraded`, `down`, `unknown` | `unknown` |
| `deployments.status` | `planned`, `approved`, `running`, `succeeded`, `failed` | `planned` |
| `incidents.severity` | `low`, `medium`, `high`, `critical` | `medium` |
| `incidents.status` | `open`, `investigating`, `resolved` | `open` |
| `agent_runs.status` | `running`, `succeeded`, `failed` | `running` |
| `messages.role` | `user`, `assistant`, `system`, `tool` | – |

## Offene Punkte

- [ ] `users`: Feld `password_hash` für den Login ergänzen
- [ ] `documents`: Feld `embedding = db.Column(Vector(1536))` mit pgvector ergänzen
- [ ] Erlaubte Werte in der API prüfen
