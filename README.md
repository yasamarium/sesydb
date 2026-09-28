# Square Era PostgreSQL Database Manager Node 1 (`sesydb`)

Primary PostgreSQL database management runner for **Square Era**. This repository coordinates live relational storage, automated continuous health monitoring, and data synchronization with `yasamarium/sedb`.

## Architecture & Responsibilities
- **Node Role**: Primary PostgreSQL Database Manager (Node 1).
- **Automation Workflow (`.github/workflows/postgres-runner.yml`)**:
  - Automatically runs every 5 hours via cron schedule (`0 */5 * * *`).
  - Spins up a dedicated PostgreSQL 15 service container.
  - Applies database schema (`schema/init.sql`) and seed data from `yasamarium/sedb`.
  - Runs the Express database management server (`manager.js`) on port 8080.
  - Exposes an encrypted public Cloudflare tunnel for external health inspection and admin queries.
  - Runs continuous health checks for 285 minutes (~4.75 hours).
  - Triggers the next workflow run before completion to achieve continuous 24/7 uptime without interruption.
- **Failover Node**: `yasamarium/sesydb2` acts as the secondary manager node with staggered restart intervals.

## REST API Endpoints
- `GET /`: Service information, operational role, and uptime metrics.
- `GET /health`: PostgreSQL connection status, pool metrics, and database timestamp.
- `GET /status`: Database storage size, table record counts (users, profiles, rooms, world blocks), and memory usage.
- `POST /sync`: Pulls the latest JSON snapshots from `yasamarium/sedb` and syncs them into PostgreSQL.
- `POST /query`: Authorized SQL query interface for administrative tasks.
