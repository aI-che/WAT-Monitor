# WAT Monitor

An **n8n workflow** that watches Summer Work Travel (SWT) webpages every 6 hours, uses AI to decide whether a change matters for the **2027 season**, and sends a **Gmail alert** only when it does.

## How it works

```text
Schedule (6h) → Fetch pages → Extract visible text → Hash → Compare with last run
                                                              │
                                              changed? → AI relevance check → Gmail alert
```

- The first run only stores a baseline (no emails).
- Ads, layout tweaks and unrelated news are filtered out by the AI step (NVIDIA-hosted, OpenAI-compatible model).

## Tech

n8n (Community Edition) · Docker · NVIDIA AI API · Gmail OAuth2

## Quick start

```bash
git clone https://github.com/aI-che/WAT-Monitor.git
cd WAT-Monitor
docker compose up -d        # n8n → http://localhost:5678
```

1. In n8n: **Workflows → Import from File → `WAT-Monitor.json`**
2. Add your **NVIDIA API** and **Gmail OAuth2** credentials to the matching nodes.
3. Edit the URL list and the schedule interval if needed, then activate.

> The exported workflow is sanitized: it contains no API keys or credentials.

## License

MIT
