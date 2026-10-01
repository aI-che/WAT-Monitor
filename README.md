# WAT 2027 Web Page Change Monitor

An n8n workflow that checks selected Summer Work Travel-related webpages every 6 hours, detects visible-content changes, asks an NVIDIA-hosted OpenAI-compatible model whether a change is relevant to the 2027 Summer Work Travel season, and sends relevant alerts by Gmail.

## Repository contents

- `WAT-Monitor.json` — sanitized n8n workflow export.
- `docker-compose.yml` — local n8n Community Edition setup.
- `.gitignore` — prevents local data and secrets from being committed.

## Important: credentials are intentionally NOT included

The workflow does not contain:

- NVIDIA API keys
- Gmail OAuth access/refresh tokens
- OAuth client secrets
- n8n credential IDs
- n8n instance metadata

After importing the workflow, create/select your own credentials in n8n:

1. **AI Analysis** — Header Auth credential:
   - Name: `Authorization`
   - Value: `Bearer YOUR_NVIDIA_API_KEY`
2. **Send Change Alert** — your Gmail OAuth2 credential.
3. In **Send Change Alert**, replace `YOUR_EMAIL@example.com` with the address that should receive alerts.

## Local setup

```bash
docker compose up -d
```

Open:

`http://localhost:5678`

Then import `WAT-Monitor.json`.

## Data Table

The workflow expects an n8n Data Table named:

`wat_page_hashes`

with these columns:

- `page_url` — String
- `content_hash` — String
- `content_text` — String
- `last_checked` — Date

## Security

Never commit API keys, OAuth tokens, `.env` files, or the n8n data volume to GitHub.

The public workflow is a sanitized copy. Keep your working n8n instance and its credentials separate from the repository.
