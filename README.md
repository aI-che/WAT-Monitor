# WAT 2027 Web Page Change Monitor

An n8n workflow that monitors selected **Summer Work Travel (SWT)**-related webpages every 6 hours, detects visible-content changes, analyzes those changes with an NVIDIA-hosted OpenAI-compatible AI model, and sends relevant alerts by Gmail.

The project is designed to run locally using **Docker and n8n Community Edition**.

---

## How It Works

The workflow follows this process:

1. Runs automatically every **6 hours**.
2. Fetches the configured webpages.
3. Extracts the visible page content.
4. Creates a hash of the extracted content.
5. Compares the current hash with the previously stored version.
6. If the page has changed, the changed content is sent to an NVIDIA-hosted AI model.
7. The AI determines whether the change is relevant to the **2027 Summer Work Travel season**.
8. Relevant changes are sent to the configured Gmail address.
9. Unrelated changes are ignored.

### First Run

On the first execution, each monitored webpage is stored as its **initial baseline**.

No notification is sent during the first run because there is no previous version to compare against.

Notifications are only generated when a later execution detects a change.

---

## Features

- Automatic monitoring every 6 hours
- Visible-content change detection
- Hash-based comparison
- AI-powered relevance filtering
- 2027 Summer Work Travel-specific analysis
- Gmail notifications
- Local n8n Community Edition deployment
- Docker-based setup
- Persistent n8n data
- Sanitized workflow export
- Configurable monitored webpages

---

## Requirements

Before starting, install or obtain:

- [Docker](https://www.docker.com/)
- Docker Compose
- An NVIDIA API key
- A Google/Gmail account
- Access to an n8n Community Edition instance

The included `docker-compose.yml` is intended for running n8n locally with Docker.

---

## Repository Contents

```text
WAT-Monitor/
├── WAT-Monitor.json
├── docker-compose.yml
├── .gitignore
└── README.md
```

### Files

#### `WAT-Monitor.json`

Sanitized n8n workflow export.

It contains the workflow logic but does **not** contain private API keys or OAuth credentials.

#### `docker-compose.yml`

Docker Compose configuration for running n8n locally.

#### `.gitignore`

Prevents local data, credentials, environment files, and other sensitive files from being accidentally committed.

#### `README.md`

Project documentation and setup instructions.

---

# Local Setup

## 1. Clone the Repository

```bash
git clone https://github.com/aI-che/WAT-Monitor.git
cd WAT-Monitor
```

---

## 2. Start n8n

Start the n8n container:

```bash
docker compose up -d
```

Check that the container is running:

```bash
docker compose ps
```

You should see the n8n container running.

To view the logs:

```bash
docker compose logs -f
```

Open n8n in your browser:

```text
http://localhost:5678
```

---

## 3. Create Your n8n Account

When opening n8n for the first time, create the local n8n owner account.

This account is used to access your local n8n instance.

---

# Import the Workflow

After opening n8n:

1. Open the n8n interface.
2. Go to **Workflows**.
3. Choose **Import from File**.
4. Select:

```text
WAT-Monitor.json
```

5. Open the imported workflow.

The workflow should appear with all configured nodes.

---

# Configure the NVIDIA API

The workflow uses an NVIDIA-hosted OpenAI-compatible model to analyze detected webpage changes.

You need an NVIDIA API key before running the workflow.

Create or obtain your API key from the NVIDIA API service.

### Important

Do **not** put your real API key directly into:

- `WAT-Monitor.json`
- `README.md`
- GitHub
- any publicly shared workflow export

The repository contains a sanitized workflow so that private credentials are not exposed.

Configure the NVIDIA credential inside n8n and assign it to the AI/HTTP node used by the workflow.

The exact credential configuration depends on the NVIDIA model and OpenAI-compatible endpoint configured in the workflow.

---

# Configure Gmail

The workflow uses Gmail to send notifications when a relevant change is detected.

## Gmail OAuth

In n8n:

1. Open the Gmail node.
2. Create or select a Gmail OAuth2 credential.
3. Sign in with the Google account that will send the alerts.
4. Grant the required permissions.
5. Select the created credential in the Gmail node.

The Gmail account must be authorized before the workflow can send notifications.

### Recommended

Use a dedicated Gmail account for monitoring notifications if you do not want alerts mixed with your personal inbox.

---

# Configure the Monitored Websites

The workflow contains a list of webpages that are checked periodically.

The monitored URLs can be changed in the workflow's URL configuration/preparation node.

Example:

```text
https://j1visa.state.gov/programs/summer-work-travel/
https://travel.state.gov/
https://www.campusedu.com.tr/work-and-travel/
```

You can add or remove URLs according to the websites you want to monitor.

### Important

Only add webpages that you are permitted to access and monitor.

Some websites may use:

- JavaScript rendering
- bot protection
- Cloudflare
- authentication
- rate limiting

Such websites may not return their complete visible content through a simple HTTP request.

---

# Monitoring Interval

By default, the workflow runs every:

```text
6 hours
```

This means the configured webpages are checked approximately four times per day.

The interval can be changed in the **Schedule Trigger** node.

For example:

```text
6 hours
```

can be changed to another interval appropriate for your monitoring needs.

---

# Change Detection

The workflow does not simply compare the entire HTML response.

It extracts the relevant visible page content and generates a hash from that content.

Conceptually:

```text
Webpage
   ↓
Visible Content
   ↓
Hash
   ↓
Previous Hash
   ↓
Compare
```

If the hashes are identical:

```text
No meaningful page-content change detected
```

If the hashes are different:

```text
Page changed
      ↓
AI analysis
      ↓
Relevant?
   ↙       ↘
 Yes       No
 ↓          ↓
Gmail     Ignore
```

This helps avoid sending an email for every scheduled check.

---

# Baseline Behavior

The first time a URL is processed, the workflow has no previous version to compare it against.

Therefore:

```text
First run
   ↓
Store current content/hash
   ↓
No email
```

On subsequent runs:

```text
Current content
      ↓
Compare with stored version
      ↓
Changed?
   ↓
 Yes
   ↓
AI relevance analysis
```

This prevents the initial setup from generating a large number of false alerts.

---

# AI Relevance Analysis

A webpage can change for many reasons that have nothing to do with the 2027 Summer Work Travel season.

For example:

- advertisements
- navigation changes
- formatting changes
- unrelated announcements
- footer updates
- minor website modifications

The AI analysis step is used to determine whether the detected change is relevant to the monitoring purpose.

The AI receives the detected change and evaluates whether it contains information relevant to:

```text
2027 Summer Work Travel
```

Examples of potentially relevant changes include:

- 2027 program announcements
- application information
- application opening dates
- program requirements
- participant requirements
- visa-related information
- official program updates
- important deadlines
- major agency announcements
- changes affecting Turkish participants

Irrelevant changes should not generate an email.

---

# Gmail Notifications

When the AI determines that a detected change is relevant, the workflow sends an email notification.

The notification should contain enough information to identify:

- which webpage changed
- what changed
- why the change may be relevant
- the relevant page URL

This allows the user to quickly open the original webpage and verify the information.

---

# Testing the Workflow

Before relying on the automatic 6-hour schedule, perform a manual test.

In n8n:

1. Open the workflow.
2. Save the workflow.
3. Execute the workflow manually.
4. Check the output of each major node.
5. Verify that webpages are fetched successfully.
6. Verify that visible content is extracted.
7. Verify that hashes are generated.
8. Verify that the baseline is stored.
9. Verify that the AI node can be called.
10. Verify that Gmail can send an email.

### First Test

The first successful run should normally create the baseline without sending an alert.

After the baseline has been created, run the workflow again.

If the monitored webpage has not changed, no notification should be generated
