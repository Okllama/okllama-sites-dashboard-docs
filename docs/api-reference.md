# API Reference

[← Back to Docs](README.md)

All API requests go to a single endpoint and are dispatched by the `action` field in the request body. Every request
must be authenticated with an API key.

---

## Base URL

```
POST /api/endpoints/
```

All requests use `POST` with a JSON body regardless of the operation.

---

## Authentication

Every request must include `id` and `secret` in the request body. Keys are created and managed in the admin interface
under **API Keys**.

| Field    | Type   | Description                              |
|----------|--------|------------------------------------------|
| `id`     | string | API key ID (prefixed with `id_`)         |
| `secret` | string | API key secret (prefixed with `secret_`) |

The API key must have a permission matching the `action` being called, or the `admin` permission to bypass all checks.

**Auth error responses:**

| Status | Meaning                                              |
|--------|------------------------------------------------------|
| `400`  | Missing `id`, `secret`, or `action`                  |
| `401`  | Invalid credentials                                  |
| `403`  | Token disabled or missing permission for this action |

---

## Common Response Fields

Operations that run in the background return a job ID immediately. Use the job ID to poll status in the admin interface.

```json
{
  "message": "Starting install process.",
  "job_id": "a1b2c3d4-..."
}
```

---

## Actions

---

### `installSite`

Installs a WordPress site by cloning a template in WHM. Runs asynchronously.

**Required permission:** `installsite`

**Request body**

| Field       | Type          | Required | Description                                    |
|-------------|---------------|----------|------------------------------------------------|
| `action`    | string        | Yes      | `installSite`                                  |
| `entry`     | string (JSON) | Yes      | Form entry data as a JSON string               |
| `form`      | string (JSON) | Yes      | Form definition as a JSON string               |
| `ticket_id` | string        | No       | WHMCS ticket ID to associate with this install |
| `dry_run`   | string        | No       | `"true"` to simulate without making changes    |
| `overwrite` | string        | No       | `"true"` to overwrite an existing installation |

**Example request**

```bash
curl -X POST https://dashboard.example.com/api/endpoints/ \
  -H "Content-Type: application/json" \
  -d '{
    "action": "installSite",
    "id": "id_...",
    "secret": "secret_...",
    "entry": "{\"form_id\": 1, ...}",
    "form": "{...}",
    "ticket_id": "12345"
  }'
```

**Example response**

```json
{
  "message": "Starting site install",
  "job_id": "a1b2c3d4-..."
}
```

---

### `addSiteDB`

Records a form entry in the database without triggering an installation. Useful for logging a sale before the install is
ready to run.

**Required permission:** `addsitedb`

**Request body**

| Field       | Type          | Required | Description                                   |
|-------------|---------------|----------|-----------------------------------------------|
| `action`    | string        | Yes      | `addSiteDB`                                   |
| `entry`     | string (JSON) | Yes      | Form entry data as a JSON string              |
| `form`      | string (JSON) | Yes      | Form definition as a JSON string              |
| `ticket_id` | string        | No       | WHMCS ticket ID to associate with this record |

**Example response**

```json
{
  "site install": 42
}
```

---

### `setupSite`

Fills a site with content from its stored form entry — sets WordPress options, page content, header/footer, and Gravity
Forms notifications. Runs asynchronously.

**Required permission:** `setupsite`

**Request body**

| Field          | Type   | Required | Description                                       |
|----------------|--------|----------|---------------------------------------------------|
| `action`       | string | Yes      | `setupSite`                                       |
| `domain`       | string | Yes      | Domain of the site to set up (e.g. `example.com`) |
| `dry_run`      | string | No       | `"true"` to log actions without applying them     |
| `skip_uploads` | string | No       | `"true"` to skip image uploads                    |

**Example request**

```bash
curl -X POST https://dashboard.example.com/api/endpoints/ \
  -H "Content-Type: application/json" \
  -d '{
    "action": "setupSite",
    "id": "id_...",
    "secret": "secret_...",
    "domain": "example.com"
  }'
```

**Example response**

```json
{
  "message": "Starting setup of example.com.",
  "job_id": "a1b2c3d4-..."
}
```

---

### `importSites`

Fetches all sites from every enabled WHM server and syncs them into the database. New sites are created; existing sites
have their title, WordPress version, and PHP version updated. Runs asynchronously.

**Required permission:** `importsites`

**Request body**

| Field    | Type   | Required | Description   |
|----------|--------|----------|---------------|
| `action` | string | Yes      | `importSites` |

**Example response**

```json
{
  "message": "Starting site import",
  "job_id": "a1b2c3d4-..."
}
```

---

### `checkDNS`

Checks DNS records for one or more sites against a stored DNS template and optionally saves results to the database.
Runs asynchronously.

**Required permission:** `checkdns`

**Request body**

| Field              | Type             | Required    | Description                                                     |
|--------------------|------------------|-------------|-----------------------------------------------------------------|
| `action`           | string           | Yes         | `checkDNS`                                                      |
| `template`         | string           | Yes         | Name of the DNS template to check against                       |
| `domains`          | array of strings | Conditional | List of domains to check. Required unless `all_sites` is `true` |
| `all_sites`        | string           | Conditional | `"true"` to check every site in the database                    |
| `save_to_database` | string           | No          | `"true"` to persist results                                     |

**Example request**

```bash
curl -X POST https://dashboard.example.com/api/endpoints/ \
  -H "Content-Type: application/json" \
  -d '{
    "action": "checkDNS",
    "id": "id_...",
    "secret": "secret_...",
    "template": "default",
    "domains": ["example.com", "example2.com"],
    "save_to_database": "true"
  }'
```

**Example response**

```json
{
  "message": "Starting DNS check on 2 domain(s)",
  "save_to_database": true,
  "job_id": "a1b2c3d4-..."
}
```

---

### `setupCaptcha`

Installs and configures reCAPTCHA Enterprise on a specific Gravity Form on a site. Runs asynchronously.

**Required permission:** `setupcaptcha`

**Request body**

| Field     | Type    | Required | Description                                 |
|-----------|---------|----------|---------------------------------------------|
| `action`  | string  | Yes      | `setupCaptcha`                              |
| `domain`  | string  | Yes      | Domain of the site                          |
| `form_id` | integer | Yes      | Gravity Forms form ID to install captcha on |

**Example response**

```json
{
  "message": "Installing captcha on example.com.",
  "job_id": "a1b2c3d4-..."
}
```

---

### `releaseSites`

Finds all sites whose WHMCS ticket matches a given status, takes them out of maintenance mode, and updates the ticket
status. Runs asynchronously.

**Required permission:** `releasesites`

**Request body**

| Field                      | Type   | Required | Description                                        |
|----------------------------|--------|----------|----------------------------------------------------|
| `action`                   | string | Yes      | `releaseSites`                                     |
| `search_ticket_status`     | string | Yes      | Ticket status to search for (e.g. `"In Progress"`) |
| `failed_ticket_status`     | string | No       | Status to set on failure (default: `"Open"`)       |
| `successful_ticket_status` | string | No       | Status to set on success (default: `"Closed"`)     |

**Example response**

```json
{
  "message": "Starting releasing sites from Maintenance Mode",
  "job_id": "a1b2c3d4-..."
}
```

---

### `getWordpressLogin`

Generates a magic login link for the `pcs-admin` user on a site.

**Required permission:** `getwordpresslogin`

**Request body**

| Field    | Type   | Required | Description         |
|----------|--------|----------|---------------------|
| `action` | string | Yes      | `getWordpressLogin` |
| `domain` | string | Yes      | Domain of the site  |

**Example request**

```bash
curl -X POST https://dashboard.example.com/api/endpoints/ \
  -H "Content-Type: application/json" \
  -d '{
    "action": "getWordpressLogin",
    "id": "id_...",
    "secret": "secret_...",
    "domain": "example.com"
  }'
```

**Example response**

```
https://example.com/?mlkey=abc123...
```

---

### `loadYootheme`

Opens the YooTheme builder on a site using Playwright. Used to trigger a builder load when the builder has not yet been
initialized. Runs asynchronously.

**Required permission:** `loadyootheme`

**Environment Requirements:**
- Requires `PLAYWRIGHT_URL` to point to a running Playwright WebSocket server
- Saves screenshots to `SCREENSHOTS_DIR` (defaults work in Docker, local dev must override)
- See [Configuration](configuration.md) for environment variable details

**Request body**

| Field    | Type   | Required | Description        |
|----------|--------|----------|--------------------|
| `action` | string | Yes      | `loadYootheme`     |
| `domain` | string | Yes      | Domain of the site |

**Example response**

```
Started load yootheme
```

---

## Error Responses

| Status | Meaning                                          |
|--------|--------------------------------------------------|
| `400`  | Bad request — missing or invalid parameters      |
| `401`  | Unauthorized — invalid API credentials           |
| `403`  | Forbidden — token disabled or missing permission |
| `404`  | Not found — requested resource does not exist    |
| `500`  | Internal server error                            |

---

## Next Steps

- [Configuration](configuration.md) — set up environment variables
- [WHM Setup](whm.md) — configure WHM servers and templates
- [Getting Started](getting-started.md) — back to setup
