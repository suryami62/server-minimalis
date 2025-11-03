# server-minimalis

## Description
This project orchestrates Nginx Proxy Manager with a PostgreSQL database backend using Docker Compose, providing an efficient and persistent proxy management solution.

## Key Features
- Configuration via environment file.
- Orchestration support through Docker Compose.
- Easy HTTP/S proxy management with Nginx Proxy Manager.
- Persistent data storage for both Nginx Proxy Manager and PostgreSQL.

## Prerequisites
To run this project, you need the following installed on your system:
- Docker (generic version)
- Docker Compose (generic version)

## Quick Start (with Docker)
Follow the steps below to start the services using Docker Compose:

1. Duplicate the [`.env.example`](.env.example) file to `.env` in the project root and adjust the environment variable values as needed.  
   Make sure to replace the default password with a strong one.

   ```bash
   cp .env.example .env
   ```

2. Run Docker Compose to start all services in detached mode (running in the background):

   ```bash
   docker compose up -d
   ```

3. Once the services are running, you can access the Nginx Proxy Manager web interface at:
   - `http://localhost:81` (Admin interface)
   - `http://localhost:80` (HTTP traffic to be proxied)
   - `https://localhost:443` (HTTPS traffic to be proxied)

4. To stop and remove all containers, networks, and volumes created by Docker Compose:

   ```bash
   docker compose down
   ```

## Environment Configuration
The following environment variables can be set in your `.env` file:

| Name              | Description                                                                                          | Example/Default Value                  |
| :---------------- | :--------------------------------------------------------------------------------------------------- | :------------------------------------- |
| `POSTGRES_DB`     | Name of the PostgreSQL database to be created and used by Nginx Proxy Manager.                       | `npm`                                  |
| `POSTGRES_USER`   | PostgreSQL database user to be created and used by Nginx Proxy Manager.                              | `npm`                                  |
| `POSTGRES_PASSWORD` | Password for the PostgreSQL database user. **IMPORTANT: Replace with a strong and unique password.** | `replace_with_a_very_strong_password`  |

## Services and Orchestration (Docker Compose)
This project defines two main services in [`docker-compose.yml`](docker-compose.yml):

### Service: `app` (Nginx Proxy Manager)
- **Image**: `jc21/nginx-proxy-manager:latest`
- **Port Mapping**:
  - `80:80` (HTTP)
  - `443:443` (HTTPS)
  - `81:81` (Web Admin UI)
- **Volumes**:
  - `./data:/data`: Stores Nginx Proxy Manager configuration data.
  - `./letsencrypt:/etc/letsencrypt`: Stores SSL/TLS certificates.
- **Environment Keys**: `DB_TYPE`, `DB_POSTGRES_HOST`, `DB_POSTGRES_PORT`, `DB_POSTGRES_USER`, `DB_POSTGRES_PASSWORD`, `DB_POSTGRES_NAME`

### Service: `db` (PostgreSQL Database)
- **Image**: `postgres:18.0-alpine`
- **Volumes**:
  - `./data/postgresql:/var/lib/postgresql/18/docker`: Stores PostgreSQL database data persistently.
- **Environment Keys**: `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`
