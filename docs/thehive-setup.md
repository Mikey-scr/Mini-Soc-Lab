\# 🐝 TheHive Setup Guide



\## Overview

TheHive is the case management platform for the Mini SOC Lab.

Every alert automatically creates a case with AI analysis included.



\---



\## Architecture



```

Wazuh Alert → n8n → Groq AI Analysis → TheHive Case Created → Discord Notified

```



\---



\## Setup Steps



\### 1. Start TheHive

```bash

cd docker/thehive

docker compose up -d

```



Wait 60 seconds then access:

```

http://localhost:9001

```



\### 2. Default Login

```

Email:    admin@thehive.local

Password: secret

```



\### 3. Create Organisation

\- Go to Admin → Organisations

\- Click + Create

\- Name: SOC

\- Description: SOC Team



\### 4. Create User

\- Go to SOC org → Users → +

\- Login: analyst@soc.local

\- Name: SOC Analyst

\- Profile: org-admin

\- Set password

\- Create API Key → copy it



\### 5. Configure n8n TheHive nodes

Update Authorization header in all 3 TheHive nodes:

```

Bearer YOUR\_API\_KEY

```



\---



\## Case Structure



Every auto-created case contains:

\- Title with severity emoji and alert description

\- Rule ID and severity level

\- Affected agent name

\- Source attacker IP

\- Timestamp

\- Full Groq AI analysis including:

&#x20; - Threat type

&#x20; - Risk assessment

&#x20; - Immediate action

&#x20; - Mitigation strategies

\- Tags: wazuh, severity, automated



\---



\## Severity Mapping



| n8n Severity | TheHive Severity | Flag |

|---|---|---|

| Critical (10+) | 3 - High | ✅ Flagged |

| High (7-9) | 2 - Medium | ❌ |

| Medium (5-6) | 1 - Low | ❌ |



\---



\## View Cases

1\. Login as analyst@soc.local

2\. Go to http://localhost:9001/cases

3\. Cases appear automatically when attacks are detected



\---



\## Troubleshooting



| Error | Fix |

|---|---|

| 401 Unauthorized | Regenerate API key |

| 403 Forbidden | Use org-level user not platform admin |

| Connection refused | Check TheHive container is running |

| Invalid JSON | AI response has special characters — check replace function |



\---



\## Key Files

\- `docker/thehive/docker-compose.yml`

\- `n8n/workflow.json`

