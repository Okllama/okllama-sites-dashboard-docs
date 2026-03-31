# Getting Started

[← Back to Docs](README.md)

This guide covers installation and first-time setup of Sites Dashboard.

## Prerequisites

- A Linux server with [Docker](https://docs.docker.com/get-docker/) and Docker Compose v2.20 or later
- A domain name pointed at your server's IP address
- A reverse proxy (if web accessibility is required)

---

## 1. Clone the repository

```bash
git clone https://github.com/your-org/sites-dashboard.git ~/docker/sites-dashboard
cd ~/docker/sites-dashboard
```

---

## 2. Configure the environment

Copy the example environment file and fill in the required values:

```bash
cp example.env .env
```

See [Configuration](configuration.md) for a description of every variable. At minimum, set a strong `DJANGO_SECRET_KEY` and `POSTGRES_PASSWORD` before starting.

---

## 3. Configure Docker Compose

Copy the example compose file:

```bash
cp example.docker-compose.yml docker-compose.yml
```

If using Traefik, open `docker-compose.yml` and update the Traefik labels on the `web` service to use your domain:

```yaml
- "traefik.http.routers.sites-dashboard.rule=Host(`dashboard.example.com`)"
- "traefik.http.routers.sites-dashboard.tls.domains[0].main=dashboard.example.com"
```

Configure any other options you need in `docker-compose.yml`

Create the external frontend network (required before starting the application):

```bash
docker network create dashboard-frontend
```

---

## 4. Set up SSH keys

The dashboard connects to WHM servers over SSH. Authorize a key to connect to your WHM server(s) and put the key pair in the `sshKeys` folder:

```bash
cp /path/to/your/private/key sshKeys/id_rsa
chmod 600 sshKeys/id_rsa
```

The `sshKeys` directory is mounted into the container at `~/.ssh`. The SSH user and port for each server are configured in the admin interface later.

---

## 5. Start the application

```bash
docker compose up -d
```

Check the logs to confirm everything started cleanly:

```bash
docker compose logs web
```

---

## 6. Create the admin user

```bash
docker exec -it sites-dashboard-web python manage.py createsuperuser
```

Then visit `http://localhost:8000/admin` and log in.

---

## Next Steps

With the application running, connect it to your servers:

- [WHM Setup](whm.md) — configure WHM servers, packages, and site templates
- [WHMCS Setup](whmcs.md) — connect WHMCS for billing and renewal automation
- [Configuration](configuration.md) — full reference for all environment variables
