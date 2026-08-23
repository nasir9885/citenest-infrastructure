
## 1. Project Root

The main CiteNest project directory is:

/home/nasir/project


## 2. Directory Structure

Applications:

/home/nasir/project/applications/citenest

Infrastructure:

/home/nasir/project/infrastructure


## 3. Check All Running Docker Services

Use:

docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"


## 4. Check AIDMS

Location:

/home/nasir/project/applications/citenest/core

Check services:

docker compose \
  -f infra/compose/compose.yaml \
  --env-file .env \
  ps


## 5. AIDMS Health Check

Run:

curl http://127.0.0.1:8000/api/v1/health/ready

Expected response:

{"status":"ready","service":"citenest-api","dependencies":{"database":"ready"}}


## 6. Start AIDMS

cd ~/project/applications/citenest/core

docker compose \
  -f infra/compose/compose.yaml \
  --env-file .env \
  up -d


## 7. Stop AIDMS

cd ~/project/applications/citenest/core

docker compose \
  -f infra/compose/compose.yaml \
  --env-file .env \
  stop


## 8. AIDMS Logs

API:

docker logs -f citenest-api-1

Web:

docker logs -f citenest-web-1

Database:

docker logs -f citenest-database-1


## 9. Authentik

Location:

/home/nasir/project/infrastructure/authentik

Check services:

cd ~/project/infrastructure/authentik

docker compose \
  -f compose.yaml \
  --env-file .env \
  ps


## 10. Start Authentik

cd ~/project/infrastructure/authentik

docker compose \
  -f compose.yaml \
  --env-file .env \
  up -d


## 11. Stop Authentik

cd ~/project/infrastructure/authentik

docker compose \
  -f compose.yaml \
  --env-file .env \
  stop


## 12. Authentik Health Check

Check the public authentication endpoint:

curl -I https://auth.citenest.in

A HTTP 302 response redirecting to the Authentik authentication flow is expected for an unauthenticated request.


## 13. Authentik Logs

Server:

docker logs -f authentik-authentik-server-1

Worker:

docker logs -f authentik-authentik-worker-1

Database:

docker logs -f authentik-authentik-database-1

## 14. Git Repositories

CiteNest uses four independent Git repositories:

- citenest-site
- citenest-ems
- citenest-aidms
- citenest-infrastructure


## 15. Development Workflow

For application development:

cd ~/project/applications/citenest/<application>

git checkout development
git pull origin development

After making changes:

git status
git add -A
git commit -m "describe the change"
git push origin development


## 16. Promote Development to Main

Only promote changes after testing.

For an application:

cd ~/project/applications/citenest/<application>

git checkout main
git pull origin main
git merge development
git push origin main
git checkout development


## 17. Check Repository Status

Website:

cd ~/project/applications/citenest/site
git status
git branch -vv

EMS:

cd ~/project/applications/citenest/ems
git status
git branch -vv

AIDMS:

cd ~/project/applications/citenest/core
git status
git branch -vv

Infrastructure:

cd ~/project/infrastructure
git status
git branch -vv


## 18. Environment Files and Secrets

Never commit real environment files.

Important local environment files include:

/home/nasir/project/infrastructure/authentik/.env
/home/nasir/project/infrastructure/database/.env

Application environment files may also contain secrets.

Use .env.example files as templates.

Never paste passwords, API keys, client secrets, database passwords, or other credentials into Git.


## 19. Docker Volumes

Important persistent volumes include:

citenest_postgres_data
citenest_document_repository
citenest_authentik_data
citenest_authentik_postgres_data

Inspect a volume:

docker volume inspect <volume-name>

Never delete these volumes unless a verified backup exists and deletion is intentional.


## 20. Dangerous Docker Commands

Do not run these commands casually:

docker volume prune

docker system prune

docker compose down -v

These commands can remove data or resources required by CiteNest.

Always inspect the affected containers and volumes before using destructive commands.


## 21. Check Docker Volumes

List volumes:

docker volume ls

List CiteNest-related volumes:

docker volume ls --format '{{.Name}}' | grep -E 'citenest|document'


## 22. Check Container Details

Example:

docker inspect citenest-api-1

Check the Compose project:

docker inspect citenest-api-1 \
  --format 'Project={{ index .Config.Labels "com.docker.compose.project" }} WorkingDir={{ index .Config.Labels "com.docker.compose.project.working_dir" }}'


## 23. Restart a Single Container

Example:

docker restart citenest-api-1

Use container-specific restarts when possible instead of restarting the entire server.


## 24. Check AIDMS Document Repository

The AIDMS API uses the persistent volume:

citenest_document_repository

The container mount is:

/var/lib/citenest/documents

Inspect the mount:

docker inspect citenest-api-1 \
  --format '{{.Name}} -> {{range .Mounts}}{{.Name}} -> {{.Destination}} {{end}}'


## 25. Check Authentik Persistent Storage

Authentik uses:

citenest_authentik_data
citenest_authentik_postgres_data

Inspect:

docker volume inspect citenest_authentik_data

docker volume inspect citenest_authentik_postgres_data


## 26. Public Authentik Verification

Run:

curl -I https://auth.citenest.in

Expected behavior for an unauthenticated request:

HTTP 302

The response should redirect to the Authentik authentication flow.

A response containing:

x-powered-by: authentik

confirms that the request reached Authentik.


## 27. AIDMS API Verification

Run:

curl http://127.0.0.1:8000/api/v1/health/ready

Expected:

{"status":"ready","service":"citenest-api","dependencies":{"database":"ready"}}


## 28. Full Service Verification

Run:

docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

Verify that the expected services are running and healthy.

Then verify:

curl http://127.0.0.1:8000/api/v1/health/ready

curl -I https://auth.citenest.in


## 29. Logs

Follow AIDMS API logs:

docker logs -f citenest-api-1

Follow AIDMS web logs:

docker logs -f citenest-web-1

Follow Authentik server logs:

docker logs -f authentik-authentik-server-1

Follow Authentik worker logs:

docker logs -f authentik-authentik-worker-1

To show recent logs without following:

docker logs --tail 100 <container-name>


## 30. Server Restart Procedure

Before restarting the server:

1. Check running containers.
2. Check application health.
3. Confirm there are no active database operations.
4. Confirm important data has been backed up.
5. Restart the server.

After the server starts:

1. Check Docker.
2. Check containers.
3. Check AIDMS health.
4. Check Authentik.
5. Check public applications.
6. Review logs if any service is unhealthy.


## 31. Safe Ubuntu Shutdown

Use:

sudo shutdown -h now

or:

sudo poweroff

Do not simply turn off the server while Docker services are running.


## 32. Basic Server Diagnostics

Check disk space:

df -h

Check memory:

free -h

Check Docker:

docker info

Check running containers:

docker ps

Check all containers:

docker ps -a


## 33. Backup Principle

CiteNest has several different types of data.

Application source code:

Git repositories.

Application databases:

PostgreSQL databases.

AIDMS documents:

citenest_document_repository.

Authentik data:

citenest_authentik_data
citenest_authentik_postgres_data.

These should be backed up independently.


## 34. Git Repository Backup

A Git bundle can preserve repository history.

Example:

cd ~/project/applications/citenest/core
git bundle create ~/citenest-aidms-backup.bundle --all

Verify the bundle:

git bundle verify ~/citenest-aidms-backup.bundle


## 35. Database Backup

Before performing database maintenance or migration, create a PostgreSQL backup.

The exact backup command depends on which PostgreSQL service and database are being backed up.

Always identify:

- Database container
- Database name
- Database user
- Backup destination

before running the backup.

Do not assume that a Docker volume alone is a sufficient backup.


## 36. Document Repository Backup

The AIDMS document repository is stored in:

citenest_document_repository

Before server migration or destructive maintenance, create a separate backup of the document repository.

The repository should not be treated as recoverable from Git.


## 37. Authentik Backup

Authentik has persistent application and database data.

Important volumes:

citenest_authentik_data
citenest_authentik_postgres_data

Back up both the Authentik database and required persistent data before major infrastructure changes.


## 38. Recovery Principle

For a complete server rebuild:

1. Install Ubuntu and Docker.
2. Restore the infrastructure repository.
3. Restore required environment files securely.
4. Restore PostgreSQL databases.
5. Restore AIDMS documents.
6. Restore Authentik data.
7. Start Authentik.
8. Verify Authentik.
9. Restore application repositories.
10. Restore application environment files.
11. Start AIDMS.
12. Start EMS.
13. Start Site.
14. Verify application health.
15. Verify public domains.


## 39. Production Change Checklist

Before making a production change:

1. Check Git status.
2. Check current branch.
3. Pull the latest changes.
4. Review the intended change.
5. Confirm a backup exists if data is affected.
6. Make the change.
7. Test locally/server-side.
8. Check Docker containers.
9. Check application health.
10. Check logs.
11. Verify the public application.
12. Document important changes.


## 40. Final Safety Rule

When unsure, do not delete or recreate containers, volumes, databases, or infrastructure.

First inspect:

docker ps
docker ps -a
docker volume ls
docker volume inspect <volume>
docker inspect <container>

Then determine exactly what will be affected before making the change.
