# Configuration

[← Back to Docs](README.md)

Configuration is managed via a `.env` file. Copy the example to get started:

```bash
cp example.env .env
```

---

## Environment Variables

### Required

| Variable | Description |
|---|---|
| `DJANGO_SECRET_KEY` | Django secret key. Generate a long random string — never reuse across environments. |
| `DJANGO_ALLOWED_HOSTS` | Comma-separated list of domains the backend will respond to (e.g., `dashboard.example.com`). |
| `DJANGO_CSRF_TRUSTED_ORIGINS` | Comma-separated list of trusted origins for CSRF protection (e.g., `https://dashboard.example.com`). |
| `DJANGO_CORS_ALLOWED_ORIGIN_REGEXES` | Comma-separated list of regex patterns for domains allowed to make API requests. |
| `POSTGRES_NAME` | PostgreSQL database name. |
| `POSTGRES_USER` | PostgreSQL username. |
| `POSTGRES_PASSWORD` | PostgreSQL password. Use a strong random value. |
| `GOOGLE_APPLICATION_CREDENTIALS` | Path to your Google service account JSON file inside the container (e.g., `/app/secrets/google-key.json`). |
| `RECAPTCHA_PROJECT_ID` | Google Cloud project ID (text ID, not the numeric one) used for reCAPTCHA Enterprise. |
| `DJANGO_TIME_ZONE` | Server timezone (e.g., `America/Chicago`, `America/New_York`, `UTC`). |

### Optional (Docker defaults)

These variables default to Docker-appropriate values. **Local developers must override these.**

| Variable | Docker Default | Description |
|---|---|---|
| `DJANGO_DEBUG` | `0` | Set to `1` to enable Django debug mode. **Never use in production.** |
| `LOGGING_LOCATION` | `/app/logs/sites-dashboard.log` | Path for log output. Defaults to `/app/logs/sites-dashboard.log` in Docker, `src/logs/sites-dashboard.log` locally. |
| `POSTGRES_HOST` | `db` | PostgreSQL hostname. In Docker, this connects to the `db` service. Local dev must set to `localhost`. |
| `POSTGRES_PORT` | `5432` | PostgreSQL port. |
| `PLAYWRIGHT_URL` | `ws://playwright:3000/` | URL for Playwright WebSocket server. In Docker, connects to the `playwright` container. Local dev must set to `ws://localhost:3000/`. |
| `SCREENSHOTS_DIR` | `/app/screenshots` | Directory for Playwright screenshots. In Docker, mounted at `/app/screenshots`. Local dev should set to relative path `screenshots/`. |

---

## Docker Deployment (Recommended)

The default configuration works out-of-the-box with Docker Compose. Only the required variables need to be set:

```env
DJANGO_SECRET_KEY='replace-with-a-long-random-string'
DJANGO_ALLOWED_HOSTS='dashboard.example.com'
DJANGO_CSRF_TRUSTED_ORIGINS='https://dashboard.example.com'
DJANGO_CORS_ALLOWED_ORIGIN_REGEXES='^https://.*\.example\.com'
DJANGO_DEBUG='0'
POSTGRES_NAME='dashboard'
POSTGRES_USER='dashboard'
POSTGRES_PASSWORD='replace-with-a-strong-password'
GOOGLE_APPLICATION_CREDENTIALS='/app/secrets/google-key.json'
RECAPTCHA_PROJECT_ID='your-google-cloud-project-id'
DJANGO_TIME_ZONE='America/Chicago'
```

**Docker defaults handle the rest:**
- `POSTGRES_HOST=db` (the database container)
- `PLAYWRIGHT_URL=ws://playwright:3000/` (the Playwright container)
- `SCREENSHOTS_DIR=/app/screenshots` (volume mount location)

---

## Local Development

When running locally without Docker, you must override the Docker defaults:

```env
DJANGO_SECRET_KEY='replace-with-a-long-random-string'
DJANGO_ALLOWED_HOSTS='localhost,127.0.0.1'
DJANGO_CSRF_TRUSTED_ORIGINS='http://localhost:8000'
DJANGO_CORS_ALLOWED_ORIGIN_REGEXES='^http://localhost'
DJANGO_DEBUG='true'
POSTGRES_NAME='dashboard'
POSTGRES_USER='dashboard'
POSTGRES_PASSWORD='replace-with-a-strong-password'
POSTGRES_HOST='localhost'  # Required: overrides Docker default 'db'
PLAYWRIGHT_URL='ws://localhost:3000/'  # Required: overrides Docker default
SCREENSHOTS_DIR='screenshots'  # Required: overrides Docker container path
GOOGLE_APPLICATION_CREDENTIALS='secrets/google-key.json'
RECAPTCHA_PROJECT_ID='your-google-cloud-project-id'
DJANGO_TIME_ZONE='America/Chicago'
```

See [DEV.md](../DEV.md) for complete local development setup instructions.

---

## Notes

- The `secrets/` directory is already in `.gitignore`. Place your Google service account JSON file there and it will not be committed.
- `DJANGO_ALLOWED_HOSTS` and `DJANGO_CSRF_TRUSTED_ORIGINS` should list the same domain(s) you configure in the Traefik labels (if using Traefik).
- Multiple values are comma-separated with no spaces (e.g., `host1.com,host2.com`).

---

## Next Steps

- [Getting Started](getting-started.md) — back to setup
- [API Reference](api-reference.md) — explore available endpoints
