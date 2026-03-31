# Sites Dashboard

A Django-based dashboard for managing websites on WHM (Web Host Manager). Provides a centralized interface for site management, WordPress integration, and automated deployment.

## Features

- WHM integration for site management
- WordPress site setup and configuration
- Automated screenshots via Playwright
- RESTful API
- Webhook support *(coming soon)*

## Quick Start

```bash
# 1. Copy and configure the environment file
cp example.env .env

# 2. Copy and configure the Docker Compose file
cp example.docker-compose.yml docker-compose.yml

# 3. Create the frontend network
docker network create dashboard-frontend

# 4. Start the application
docker compose up -d

# 5. Create your admin user
docker exec -it sites-dashboard-web python manage.py createsuperuser
```

Then visit `http://localhost:8000/admin`.

## Documentation

Full setup and configuration guides are in the [`docs/`](docs/) folder:

| Guide | Description |
|-------|-------------|
| [Getting Started](docs/getting-started.md) | Prerequisites, installation, first steps |
| [Configuration](docs/configuration.md) | Environment variables and options |
| [API Reference](docs/api-reference.md) | Available API endpoints |

## License

Proprietary software. All rights reserved.
