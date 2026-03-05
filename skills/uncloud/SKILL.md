---
name: uncloud
description: Deploy applications and provision PostgreSQL databases using the uncloud CLI. Use when the user asks to deploy their app, provision a database, add a database to their app, attach a database, get database connection strings, or print .env values for database credentials.
---

# Uncloud Deploy & Database

Deploy applications and provision PostgreSQL databases via the uncloud CLI. The agent runs `uncloud` commands in the project directory.

## When to Apply

- "Deploy this app" / "Deploy my project" / "Push to production"
- "Add a database" / "Provision a database" / "Create a Postgres database"
- "Attach database to my app" / "Connect my app to a database"
- "Get database credentials" / "Print .env for database" / "Database connection string"

## Prerequisites

- **uncloud CLI** must be installed and on PATH
- **First-time setup**: User runs `uncloud deploy` once; it prompts for API URL and token, saves to `~/.uncloud/config`
- **Project config**: `.uncloud/config.json` exists (created by first deploy) with `app_id`, `name`, `project_id`

## Commands

### Deploy

Deploy the current project. Creates app on first run; subsequent runs trigger build.

```bash
uncloud deploy
```

With options:
```bash
uncloud deploy -name myapp -project proj_default
```

**Run from**: Project root (directory with source code)

### Add Database

Create a PostgreSQL database for the app and print `.env` values. Requires existing app (run deploy first).

```bash
uncloud db add
```

Options:
- `-workload dev` (default) — single replica, fast setup
- `-workload production` — 3 replicas, HA, connection pooler
- `-storage 20` — storage in GB (default 10)

Examples:
```bash
uncloud db add
uncloud db add -workload production
uncloud db add -storage 20
```

### Get Database Env Vars

Print `.env` values for the existing app database (no creation).

```bash
uncloud db env
```

Output format:
```
DATABASE_URL=postgresql://...
DATABASE_URL_READ_ONLY=...   # if production pooler
DB_HOST=...
DB_PORT=5432
DB_USER=...
DB_PASSWORD=...
DB_NAME=...
```

## Workflow

### Deploy flow
1. Ensure in project root
2. Run `uncloud deploy`
3. If first run: user may need to enter API URL (default `http://localhost:8080`) and token
4. Build submitted; status refreshes until Ctrl+C

### Database flow
1. Ensure app exists (`.uncloud/config.json` has `app_id`)
2. Run `uncloud db add` to create DB and get credentials
3. Or run `uncloud db env` if DB already exists
4. User copies printed `.env` lines into their `.env` file

## API Configuration

- **Default API**: `http://localhost:8080` (main backend) or `http://localhost:8081` (oss-apps)
- **Env overrides**: `UNCLOUD_API_URL`, `UNCLOUD_API_TOKEN`
- **Project header**: `X-Project-ID` sent when `project_id` is in config

## Troubleshooting

| Error | Solution |
|-------|----------|
| "no app in this directory" | Run `uncloud deploy` first |
| "no database attached" | Run `uncloud db add` |
| "create database failed" | Check API is running; verify K8s/DB module for production |
| Connection refused | Set `UNCLOUD_API_URL` to correct backend URL |
