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

### Optional

| Variable | Default | Description |
|---|---|---|
| `DJANGO_DEBUG` | `0` | Set to `1` to enable Django debug mode. **Never use in production.** |
| `LOGGING_LOCATION` | *(container default)* | Custom path for log output. Not needed when running in the provided Docker container. |

---

## Example `.env`

Minimal configuration for a production deployment:

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

---

## Notes

- The `secrets/` directory is already in `.gitignore`. Place your Google service account JSON file there and it will not be committed.
- `DJANGO_ALLOWED_HOSTS` and `DJANGO_CSRF_TRUSTED_ORIGINS` should list the same domain(s) you configure in the Traefik labels (if using Traefik).
- Multiple values are comma-separated with no spaces (e.g., `host1.com,host2.com`).

---

## Next Steps

- [Getting Started](getting-started.md) — back to setup
- [API Reference](api-reference.md) — explore available endpoints
