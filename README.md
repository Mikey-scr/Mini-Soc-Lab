\# 🛡️ Mini SOC Lab — Sentrix Hackathon



\[!\[Docker](https://img.shields.io/badge/Docker-Compose-blue)](https://docker.com)

\[!\[Wazuh](https://img.shields.io/badge/SIEM-Wazuh-orange)](https://wazuh.com)

\[!\[Authentik](https://img.shields.io/badge/IAM-Authentik-purple)](https://goauthentik.io)

\[!\[TheHive](https://img.shields.io/badge/Case-TheHive-yellow)](https://thehive-project.org)

\[!\[n8n](https://img.shields.io/badge/SOAR-n8n-red)](https://n8n.io)

\[!\[Groq](https://img.shields.io/badge/AI-Groq%20LLaMA-green)](https://groq.com)



A fully automated, open-source Security Operations Center built for the Sentrix Hackathon. Real data flows, real alerts fire, real automated responses execute — zero human intervention after attack trigger.



\---



\## 🏗️ Architecture

┌─────────────────────────────────────────────────────────────┐



│                     MINI SOC LAB                            │



│                                                             │



│  ┌──────────┐    ┌──────────┐    ┌──────────┐             │



│  │  Kali    │───▶│Suricata  │───▶│  Wazuh   │             │



│  │ Attacker │    │   IDS    │    │  SIEM    │             │



│  └──────────┘    └──────────┘    └────┬─────┘             │



│                                       │                     │



│  ┌──────────┐                   ┌─────▼─────┐             │



│  │   WAF    │                   │    n8n    │             │



│  │ModSec    │                   │   SOAR   │             │



│  └──────────┘                   └─────┬─────┘             │



│                                       │                     │



│  ┌──────────┐    ┌──────────┐    ┌────▼─────┐             │



│  │Authentik │    │ TheHive  │◀───│ Groq AI  │             │



│  │   IAM    │    │  Cases   │    │ Analysis │             │



│  └──────────┘    └──────────┘    └──────────┘             │



└─────────────────────────────────────────────────────────────┘


---



\## 🔥 4 Mandatory Layers



| Layer | Tool | Status |

|---|---|---|

| 🛡️ Perimeter \& Detection | Suricata + Kali + Metasploitable | ✅ Active |

| 📊 SIEM \& Visibility | Wazuh + OpenSearch Dashboard | ✅ Active |

| 🔐 Identity \& Access | Authentik SSO + RBAC | ✅ Active |

| ⚡ Automated Response | n8n + Groq AI + Wazuh Active Response | ✅ Active |



\---



\## 🚀 Full Stack



| Component | Tool | Port |

|---|---|---|

| SIEM | Wazuh Manager + Indexer + Dashboard | 80, 9200 |

| IAM | Authentik | 9000 |

| Case Management | TheHive | 9001 |

| SOAR | n8n | 5678 |

| WAF | ModSecurity + Nginx | 8080 |

| AI Analysis | Groq LLaMA 3.1 | API |

| Notifications | Discord Webhook | - |



\---



\## 🔄 Automated Response Pipeline


Attack Happens



↓



Suricata / Wazuh Detects



↓



n8n Webhook Receives Alert



↓



Severity Switch (Critical / High / Medium)



↓



Groq AI Analysis (Threat + Risk + Action + Mitigation)



↓



TheHive Case Created Automatically



↓



Discord Alert Sent (Color coded by severity)



↓



Wazuh Active Response — IP Blocked (Critical only)


---



\## ⚡ Quick Start



\### Prerequisites

\- Docker Desktop installed

\- 8GB RAM minimum

\- Windows 10/11 or Linux



\### 1. Clone the repo

```bash

git clone https://github.com/Mikey-scr/Mini-Soc-Lab.git

cd Mini-Soc-Lab

```



\### 2. Start Wazuh Stack

```bash

cd docker/wazuh

docker compose up -d

```



\### 3. Start Authentik

```bash

cd docker/authentik

docker compose up -d

```



\### 4. Start TheHive

```bash

cd docker/thehive

docker compose up -d

```



\### 5. Import n8n Workflow


Open http://localhost:5678

Click top right menu → Import from JSON

Upload n8n/workflow.json

Activate the workflow


---



\## 🌐 Access Services



| Service | URL | Default Login |

|---|---|---|

| Wazuh Dashboard | http://localhost | SSO via Authentik |

| Authentik | http://localhost:9000 | akadmin / your-password |

| TheHive | http://localhost:9001 | admin@thehive.local / secret |

| n8n | http://localhost:5678 | - |

| ModSecurity WAF | http://localhost:8080 | - |



\---



\## 🧪 Test the Pipeline



\### Critical Alert (Level 10)

```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" -Method POST -ContentType "application/json" -Body '{"body":{"body":{"rule":{"id":"5710","description":"SSH Brute Force Attack Detected","level":10},"agent":{"name":"metasploitable"},"data":{"srcip":"192.168.56.101"},"timestamp":"2026-06-25T09:00:00"}}}'

```



\### High Alert (Level 8)

```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" -Method POST -ContentType "application/json" -Body '{"body":{"body":{"rule":{"id":"5712","description":"SQL Injection Attack Detected","level":8},"agent":{"name":"metasploitable"},"data":{"srcip":"192.168.56.102"},"timestamp":"2026-06-25T09:00:00"}}}'

```



\### Medium Alert (Level 6)

```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" -Method POST -ContentType "application/json" -Body '{"body":{"body":{"rule":{"id":"5713","description":"Port Scan Activity Detected","level":6},"agent":{"name":"metasploitable"},"data":{"srcip":"192.168.56.103"},"timestamp":"2026-06-25T09:00:00"}}}'

```



\### WAF Attack Test

```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" -Method POST -ContentType "application/json" -Body '{"body":{"body":{"rule":{"id":"981176","description":"ModSecurity WAF: SQL Injection Attack Blocked","level":10},"agent":{"name":"modsecurity-waf"},"data":{"srcip":"192.168.56.101"},"timestamp":"2026-06-25T10:00:00"}}}'

```



\---



\## 🔐 IAM Layer — Authentik SSO



\- OpenID Connect integration with Wazuh Dashboard

\- Role-based access control (Admin / Read-only)

\- Single Sign-On — login once, access everything

\- Group-based permissions mapped to Wazuh roles



\---



\## 🤖 AI Analysis — Groq LLaMA 3.1



Every alert is analyzed by Groq AI providing:

1\. \*\*Threat Type\*\* — What kind of attack is this

2\. \*\*Risk Assessment\*\* — Impact and urgency level

3\. \*\*Immediate Action\*\* — What to do right now

4\. \*\*Mitigation\*\* — Preventive measures to stop recurrence



\---



\## 📊 Discord Alerts



Color coded severity alerts sent to Discord:

\- 🔴 \*\*Critical\*\* — Full auto response + IP block

\- 🟠 \*\*High\*\* — Case created + team notified

\- 🟡 \*\*Medium\*\* — Logged for scheduled review



\---



\## 📁 Repository Structure

mini-soc-lab/



├── README.md



├── architecture/



│   └── architecture-diagram.png



├── docker/



│   ├── wazuh/



│   │   └── docker-compose.yml



│   ├── authentik/



│   │   └── docker-compose.yml



│   ├── thehive/



│   │   └── docker-compose.yml



│   └── modsecurity/



│       └── docker-compose.yml



├── config/



│   ├── wazuh\_dashboard/



│   │   └── opensearch\_dashboards.yml



│   └── wazuh\_indexer/



│       └── config.yml



├── n8n/



│   └── workflow.json



└── docs/



├── setup-guide.md



├── authentik-sso.md



├── thehive-setup.md



├── modsecurity-waf.md



└── testing.md

