# CiteNest Server Architecture
## 1. Project Root

/home/nasir/project

    ├── infrastructure
    │   ├── authentik
    │   │   ├── compose.yaml
    │   │   ├── .env
    │   │   └── .env.example
    │   │
    │   └── database
    │       ├── compose.yaml
    │       ├── .env
    │       └── .env.example
    │
    └── applications
        └── citenest
            ├── site
            ├── ems
            └── core


## 2. CiteNest Applications

CiteNest consists of three independent applications.


### 2.1 Website — Site

Location:

/home/nasir/project/applications/citenest/site

Git repository:

git@github.com:nasir9885/citenest-site.git

Purpose:

- Public CiteNest website
- Landing page
- Enquiry page
- Website-related functionality

Branches:

- main
- development


### 2.2 Employee Management System — EMS

Location:

/home/nasir/project/applications/citenest/ems

Git repository:

git@github.com:nasir9885/citenest-ems.git

Purpose:

- Employee master data
- Employee details
- Attendance
- Salary management
- Bank salary export
- English and Arabic support

Branches:

- main
- development


### 2.3 AI Document Management System — AIDMS

Location:

/home/nasir/project/applications/citenest/core

Git repository:

git@github.com:nasir9885/citenest-aidms.git

Purpose:

- AI-powered document management
- Document repository
- Document upload
- Document-grounded querying
- AI-assisted information retrieval
- OIDC authentication through Authentik

Branches:

- main
- development


## 3. Infrastructure

Infrastructure contains shared services used by the CiteNest applications.


### 3.1 Authentik

Location:

/home/nasir/project/infrastructure/authentik

Git repository:

git@github.com:nasir9885/citenest-infrastructure.git

Purpose:

- Central identity provider
- User authentication
- OIDC
- Authentication integration for CiteNest applications

Compose services:

- authentik-database
- authentik-server
- authentik-worker

Public domain:

https://auth.citenest.in


### 3.2 Database

Location:

/home/nasir/project/infrastructure/database

Purpose:

- PostgreSQL infrastructure
- Database services used by CiteNest applications


## 4. Docker Services

### AIDMS

- citenest-web-1
- citenest-api-1
- citenest-database-1

### Authentik

- authentik-authentik-server-1
- authentik-authentik-worker-1
- authentik-authentik-database-1

### EMS / Site Database

- citenest-db

### Nextcloud

- nextcloud-app-1
- nextcloud-db-1


## 5. Persistent Docker Volumes

Important CiteNest volumes:

- citenest_postgres_data
- citenest_document_repository
- citenest_authentik_data
- citenest_authentik_postgres_data

Additional existing volumes:

- citenest-phase4-ollama-models
- citenest-phase4-qdrant-data
- citenest-site_citenest_site_db_data
- nextcloud_db
- nextcloud_nextcloud

Never remove persistent volumes unless a backup and recovery plan has been confirmed.


## 6. Authentication Architecture

Authentik is shared infrastructure and is independent from the individual applications.

The applications integrate with Authentik using OpenID Connect (OIDC).

AIDMS uses OIDC configuration including:

- CITENEST_OIDC_ISSUER
- CITENEST_OIDC_AUTHORIZATION_URL
- CITENEST_OIDC_TOKEN_URL
- CITENEST_OIDC_JWKS_URL
- CITENEST_OIDC_END_SESSION_URL
- CITENEST_OIDC_BACKCHANNEL_HOST
- CITENEST_OIDC_CLIENT_ID
- CITENEST_OIDC_CLIENT_SECRET
- CITENEST_OIDC_REDIRECT_URI
- CITENEST_OIDC_POST_LOGOUT_URL

Secrets are stored in local .env files and must never be committed to Git.

## 7. Git Repositories

CiteNest uses four independent Git repositories:

- citenest-site
- citenest-ems
- citenest-aidms
- citenest-infrastructure

Application repositories use:

- development

for development and testing.

Production-ready changes are promoted to:

- main

Infrastructure currently uses:

- main


## 8. Git Repository Ownership

applications/citenest/site
    → citenest-site

applications/citenest/ems
    → citenest-ems

applications/citenest/core
    → citenest-aidms

infrastructure
    → citenest-infrastructure

Each repository has independent Git history.


## 9. Application and Infrastructure Separation

The architecture intentionally separates applications from shared infrastructure.

Applications:

applications/
└── citenest/
    ├── site
    ├── ems
    └── core

Infrastructure:

infrastructure/
├── authentik
└── database

Shared services should not be moved into an individual application repository without an architectural review.


## 10. Environment Files and Secrets

Real environment files are local-only:

infrastructure/authentik/.env
infrastructure/database/.env

They must never be committed to Git.

Safe templates are provided as:

infrastructure/authentik/.env.example
infrastructure/database/.env.example

Application secrets such as .env.local must also remain outside Git where appropriate.

## 11. Current Authentication Flow

The general authentication architecture is:

User
  │
  ▼
CiteNest Application
  │
  │ OIDC
  ▼
Authentik
  │
  ▼
Authentication / Identity

Authentik is therefore a shared identity service rather than part of AIDMS, EMS, or the public website.


## 12. Current Server Architecture

/home/nasir/project
│
├── infrastructure
│   │
│   ├── authentik
│   │   ├── compose.yaml
│   │   ├── .env
│   │   └── .env.example
│   │
│   └── database
│       ├── compose.yaml
│       ├── .env
│       └── .env.example
│
└── applications
    │
    └── citenest
        │
        ├── site
        │   └── .git
        │
        ├── ems
        │   └── .git
        │
        └── core
            └── .git


## 13. Important Operational Principles

1. Keep applications and infrastructure separate.
2. Keep Authentik independent from individual applications.
3. Never commit secrets.
4. Never remove persistent Docker volumes without a verified backup.
5. Develop application changes on development.
6. Test before promoting changes to main.
7. Keep infrastructure changes documented.
8. Verify application health after infrastructure changes.
9. Maintain backups of application repositories, databases, and document repositories.
10. Avoid destructive Docker commands unless their impact is understood.


## 14. Current State

The server has successfully completed the CiteNest application and infrastructure separation.

Current application repositories:

- citenest-site
- citenest-ems
- citenest-aidms

Current infrastructure repository:

- citenest-infrastructure

Authentik has been moved out of AIDMS and is now managed from:

/home/nasir/project/infrastructure/authentik

The existing Authentik persistent volumes are preserved.

AIDMS is independently managed from:

/home/nasir/project/applications/citenest/core

The system should now be treated as the stable baseline for future CiteNest development.

## 15. Verified Database Architecture

The following database mapping has been verified directly from the running Docker containers and application configuration.

### 15.1 Site Database

Application:

/home/nasir/project/applications/citenest/site

Database:

citenest_site

PostgreSQL container:

citenest-db

Persistent volume:

citenest-site_citenest_site_db_data

Application connection:

DB_HOST=127.0.0.1
DB_PORT=5433
DB_NAME=citenest_site
DB_USER=citenest_site


### 15.2 EMS Database

Application:

/home/nasir/project/applications/citenest/ems

Database:

citenest_ems

PostgreSQL container:

citenest-db

Persistent volume:

citenest-site_citenest_site_db_data

Application connection:

DB_HOST=127.0.0.1
DB_PORT=5433
DB_NAME=citenest_ems
DB_USER=citenest_site


### 15.3 AIDMS Database

Application:

/home/nasir/project/applications/citenest/core

Database:

citenest_aidms

PostgreSQL container:

citenest-database-1

Persistent volume:

citenest_postgres_data

Internal Docker database connection:

database:5432

Database user:

citenest


### 15.4 Authentik Database

Service:

/home/nasir/project/infrastructure/authentik

Database:

authentik

PostgreSQL container:

authentik-authentik-database-1

Persistent volume:

citenest_authentik_postgres_data

Database user:

authentik


### 15.5 Non-Database Persistent Data

AIDMS document repository:

citenest_document_repository

Container mount:

/var/lib/citenest/documents

Authentik application data:

citenest_authentik_data

Container mount:

/data


### 15.6 Database Architecture Summary

citenest-db
│
├── citenest_site
│   └── CiteNest Site
│
└── citenest_ems
    └── Employee Management System


citenest-database-1
│
└── citenest_aidms
    └── AI Document Management System


authentik-authentik-database-1
│
└── authentik
    └── Authentik Identity Provider


### 15.7 Backup Requirement

Each application database must be backed up independently even when multiple databases share the same PostgreSQL container.

The following databases therefore require separate logical backups:

- citenest_site
- citenest_ems
- citenest_aidms
- authentik

Persistent non-database data must also be backed up separately.
