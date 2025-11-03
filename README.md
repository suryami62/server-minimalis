# Server Minimalis: Nginx Proxy Manager with PostgreSQL

This project provides a robust setup for **Nginx Proxy Manager** using Docker Compose, with **PostgreSQL** as a persistent database backend. It's designed for easy deployment, efficient management, and long-term stability.

Think of this setup as a smart, organized receptionist for all your web services. Instead of exposing multiple applications on different ports, you get a single, secure entry point that intelligently routes traffic to the correct service based on the domain name. The PostgreSQL database ensures this receptionist never loses its memory, keeping all your proxy configurations safe and sound, even after a restart.

---

## Key Features

- **Simplified Proxy Management**: Easily manage all your HTTP/S proxy hosts through a user-friendly web interface.
- **Persistent & Reliable**: Uses a PostgreSQL backend to ensure your configurations are stored safely and are not lost when containers are updated or restarted.
- **Automated Setup with Docker Compose**: Get everything running with just two commands.
- **Secure by Default**: Includes [CrowdSec](https://crowdsec.net/) for intrusion prevention, monitoring logs, and blocking malicious traffic.
- **Easy Configuration**: All settings are managed through a simple `.env` file.
- **Let's Encrypt Integration**: Generate and manage free SSL/TLS certificates directly from the web UI.

---

## Prerequisites

To run this project, you need the following installed on your system:
- **Docker**: [Installation Guide](https://docs.docker.com/get-docker/)
- **Docker Compose**: [Installation Guide](https://docs.docker.com/compose/install/)

---

## Quick Start Guide

Follow these steps to get your Nginx Proxy Manager instance up and running.

### 1. Configure Your Environment

First, create a `.env` file by copying the provided example. This file will hold your database credentials.

```bash
cp .env.example .env
```

Next, open the `.env` file and **change the default password** to something strong and unique.

```dotenv
# .env
POSTGRES_DB=npm
POSTGRES_USER=npm
POSTGRES_PASSWORD=your_very_strong_and_secret_password
```

### 2. Launch the Services

Run Docker Compose to build and start all the services in detached mode (`-d`), which runs them in the background.

```bash
docker-compose up -d
```

- **What does this do?** Docker Compose reads the `docker-compose.yml` file, pulls the required Docker images (`nginx-proxy-manager`, `postgres`, `crowdsec`), and creates the containers, networks, and volumes as defined.
- **What to expect?** You will see output indicating that the containers are being created and started. Once complete, your terminal will be free.

### 3. Access the Admin UI

Once the services are running, you can access the Nginx Proxy Manager web interface.

- **Admin UI**: `http://localhost:81`

The default administrator credentials are:
- **Email**: `admin@example.com`
- **Password**: `changeme`

You will be prompted to change these credentials upon your first login.

### 4. Stop the Services

To stop and remove all containers, networks, and volumes created by this setup, run:

```bash
docker-compose down
```
> **Note:** This command will also delete the PostgreSQL data volume, permanently removing your Nginx Proxy Manager configuration unless you have backed it up.

---

## Example: Proxying a Local Service

Here’s a simple example of how to proxy a service running on your local machine or another Docker container.

Let's say you have a simple web service running on `http://localhost:8080`. You want to make it accessible through `http://my-app.local`.

1.  **Log in** to the Nginx Proxy Manager admin UI (`http://localhost:81`).
2.  Navigate to **Hosts** > **Proxy Hosts**.
3.  Click **Add Proxy Host**.
4.  Fill in the details:
    -   **Domain Names**: `my-app.local`
    -   **Scheme**: `http`
    -   **Forward Hostname / IP**: `host.docker.internal` (to access the host machine from a Docker container) or the name of your service's container if it's on the same `app-network`.
    -   **Forward Port**: `8080`
5.  Click **Save**.

Now, if you have set up your local machine's `hosts` file to point `my-app.local` to `127.0.0.1`, you can access your service by visiting `http://my-app.local` in your browser.

---

## Troubleshooting Common Issues

Here are a few common problems you might encounter and how to solve them.

### Ports 80, 443, or 81 are already in use.

This is a common issue if you have another web server (like Apache or another Nginx instance) running on your machine.

- **Solution**: Stop the other service or change the port mappings in the `docker-compose.yml` file. For example, to map host port `8080` to container port `80`, change `- "80:80"` to `- "8080:80"`.

    ```yaml
    # docker-compose.yml
    ports:
      - "8080:80" # HTTP
      - "8443:443" # HTTPS
      - "8181:81"  # Admin UI
    ```

### Services fail to start.

- **Solution**: Check the logs to diagnose the issue. You can view the logs for a specific service by running:

    ```bash
    # Check the logs for the Nginx Proxy Manager app
    docker-compose logs app

    # Check the logs for the PostgreSQL database
    docker-compose logs db
    ```
    Look for any error messages that indicate what might be wrong (e.g., permission errors, configuration issues).

### I forgot my admin password.

- **Solution**: This is a more complex issue. The recommended approach is to refer to the [official Nginx Proxy Manager documentation](https://nginxproxymanager.com/guide/#forgot-password) for instructions on how to reset your password. It typically involves accessing the database directly, which should be done with caution.

---

## Service Orchestration

This project uses Docker Compose to define and manage three services:

| Service               | Image                               | Purpose                                                              |
| :-------------------- | :---------------------------------- | :------------------------------------------------------------------- |
| **`app`**             | `jc21/nginx-proxy-manager:latest`   | The main Nginx Proxy Manager application and web UI.                 |
| **`db`**              | `postgres:18.0-alpine`              | A dedicated PostgreSQL database for persistent configuration storage. |
| **`crowdsec`**        | `crowdsecurity/crowdsec:slim`       | An intrusion prevention system to block malicious actors.            |

These services communicate over a private internal network (`npm-network`), ensuring the database is not exposed publicly. An external network (`app-network`) is also defined, which you can attach other containers to for them to be proxied by Nginx Proxy Manager.

## Environment Variables

The following environment variables are used for configuring the PostgreSQL database. They should be set in your `.env` file.

| Name                | Description                                                               | Default Value                         |
| :------------------ | :------------------------------------------------------------------------ | :------------------------------------ |
| `POSTGRES_DB`       | The name of the PostgreSQL database.                                      | `npm`                                 |
| `POSTGRES_USER`     | The username for the PostgreSQL database.                                 | `npm`                                 |
| `POSTGRES_PASSWORD` | The password for the PostgreSQL user. **Change this to a strong password.** | `replace_with_a_very_strong_password` |
