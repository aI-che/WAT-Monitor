# WAT 2027 Web Page Change Monitor

An n8n workflow that checks selected Summer Work Travel-related webpages every 6 hours, detects visible-content changes, asks an NVIDIA-hosted OpenAI-compatible model whether a change is relevant to the 2027 Summer Work Travel season, and sends relevant alerts by Gmail.

## How it works

The workflow follows this process:

1. Runs every 6 hours.
2. Fetches the configured webpages.
3. Extracts and hashes their visible content.
4. Compares the current content with the previously stored version.
5. If a page has changed, sends the change to an NVIDIA-hosted AI model.
6. The AI determines whether the change is relevant to the 2027 Summer Work Travel season.
7. Relevant changes are sent by Gmail.

On the first run, pages are stored as the initial baseline and no alert is sent for those pages.

## Requirements

Before starting, install:

- Docker
- Docker Compose
- An n8n Community Edition instance
- An NVIDIA API key for the AI analysis
- A Google/Gmail account for email alerts

The workflow is designed to run locally with Docker.

## Repository contents

- `WAT-Monitor.json` — sanitized n8n workflow export.
- `docker-compose.yml` — local n8n Community Edition setup.
- `.gitignore` — prevents local data and secrets from being committed.

## Local setup

Clone the repository:

```bash
git clone https://github.com/aI-che/WAT-Monitor.git
cd WAT-Monitor
