\# 🛡️ ModSecurity WAF Setup Guide



\## Overview

ModSecurity with OWASP Core Rule Set (CRS) acts as the perimeter

defense layer — blocking malicious HTTP traffic before it reaches

the target application.



\---



\## Architecture



```

Attacker → ModSecurity WAF → DVWA/Target App

&#x20;              ↓

&#x20;         Block + Log

&#x20;              ↓

&#x20;         Wazuh SIEM

&#x20;              ↓

&#x20;           n8n SOAR

&#x20;              ↓

&#x20;         Discord Alert

```



\---



\## Setup



\### 1. Start ModSecurity

```bash

cd docker/modsecurity

docker compose up -d

```



Access at: http://localhost:8080



\### 2. Verify WAF is running

```bash

docker ps | grep modsecurity

```



\### 3. Test WAF blocking

```bash

\# SQL Injection attempt — should be blocked with 403

curl "http://localhost:8080/?id=1' OR '1'='1"



\# XSS attempt — should be blocked

curl "http://localhost:8080/?q=<script>alert(1)</script>"



\# Path traversal — should be blocked

curl "http://localhost:8080/?file=../../etc/passwd"

```



\---



\## What ModSecurity Blocks



| Attack Type | OWASP CRS Rule | Response |

|---|---|---|

| SQL Injection | 942100-942999 | 403 Blocked |

| XSS | 941100-941999 | 403 Blocked |

| Command Injection | 932100-932999 | 403 Blocked |

| Path Traversal | 930100-930999 | 403 Blocked |

| Scanner Detection | 913100-913999 | 403 Blocked |

| Protocol Attack | 921100-921999 | 403 Blocked |



\---



\## Log Integration with Wazuh



ModSecurity logs are mounted into Wazuh Manager:

```yaml

volumes:

&#x20; - A:/SOC/modsecurity/logs:/var/log/modsecurity

```



Wazuh automatically parses these logs and generates alerts

visible in the SIEM dashboard as a separate source.



\---



\## Log Location

```

A:\\SOC\\modsecurity\\logs\\

```



\---



\## Simulate WAF Attack for Demo



\### SQL Injection blocked by WAF:

```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" `

&#x20; -Method POST -ContentType "application/json" `

&#x20; -Body '{"body":{"body":{"rule":{"id":"981176","description":"ModSecurity WAF: SQL Injection Attack Blocked","level":10},"agent":{"name":"modsecurity-waf"},"data":{"srcip":"192.168.56.101"},"timestamp":"2026-06-25T10:00:00"}}}'

```



\---



\## Troubleshooting



| Issue | Fix |

|---|---|

| WAF not blocking | Check CRS rules are loaded |

| Logs not appearing | Verify volume mount path |

| 502 Bad Gateway | Check backend app is running |

| False positives | Tune CRS paranoia level |



\---



\## Key Files

\- `docker/modsecurity/docker-compose.yml`

