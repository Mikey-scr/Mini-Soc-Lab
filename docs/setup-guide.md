\# 🚀 Setup Guide



\## Prerequisites

\- Docker Desktop installed and running

\- 8GB RAM minimum

\- Windows 10/11

\- Git installed



\## Step 1 — Clone Repository

```bash

git clone https://github.com/Mikey-scr/Mini-Soc-Lab.git

cd Mini-Soc-Lab

```



\## Step 2 — Start Wazuh Stack

```bash

cd docker/wazuh

docker compose up -d

```

Wait 60 seconds for indexer to initialize.



Access at: http://localhost



\## Step 3 — Start Authentik

```bash

cd docker/authentik

docker compose up -d

```

Access at: http://localhost:9000



Default credentials:

\- Email: akadmin

\- Password: set on first login



\## Step 4 — Configure Authentik SSO

1\. Login to Authentik admin

2\. Create application SOC-DASH

3\. Create OAuth2/OpenID provider

4\. Set redirect URI: http://192.168.56.107/auth/openid/login

5\. Copy Client ID and Client Secret



\## Step 5 — Configure Wazuh Dashboard

Edit config/wazuh\_dashboard/opensearch\_dashboards.yml:

\- Set client\_id from Authentik

\- Set client\_secret from Authentik

\- Set connect\_url to Authentik OpenID endpoint



\## Step 6 — Start TheHive

```bash

cd docker/thehive

docker compose up -d

```

Access at: http://localhost:9001



Default credentials:

\- Email: admin@thehive.local

\- Password: secret



\## Step 7 — Configure n8n

1\. Open http://localhost:5678

2\. Import n8n/workflow.json

3\. Update TheHive API key

4\. Update Discord webhook URL

5\. Update Groq API key

6\. Activate workflow



\## Step 8 — Start ModSecurity WAF

```bash

cd docker/modsecurity

docker compose up -d

```

Access at: http://localhost:8080



\## Step 9 — Verify Everything

```bash

docker ps

```

All containers should show "Up" status.



\## Step 10 — Test Pipeline

Run test commands from docs/testing.md

