# CiteNest Infrastructure

Shared infrastructure for the CiteNest applications.

## Structure

```text
infrastructure/
├── authentik/
│   ├── compose.yaml
│   ├── .env
│   └── .env.example
│
└── database/
    ├── compose.yaml
    ├── .env
    └── .env.example
```

## Services

### Authentik

Central identity and authentication provider for CiteNest applications.

### Database

PostgreSQL infrastructure used by CiteNest applications.

## Important

Real `.env` files contain secrets and must never be committed.
Use the corresponding `.env.example` file as a template when configuring a new server.
