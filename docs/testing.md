\# 🧪 Testing Guide



\## Overview

Complete testing guide for the Mini SOC Lab pipeline.

All tests simulate real attacks and verify end-to-end automation.



\---



\## Prerequisites

\- All containers running: `docker ps`

\- n8n workflow active: http://localhost:5678

\- Discord webhook configured

\- TheHive running: http://localhost:9001



\---



\## Test 1 — Critical Alert (SSH Brute Force)



```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" `

&#x20; -Method POST -ContentType "application/json" `

&#x20; -Body '{"body":{"body":{"rule":{"id":"5710","description":"SSH Brute Force Attack Detected","level":10},"agent":{"name":"metasploitable"},"data":{"srcip":"192.168.56.101"},"timestamp":"2026-06-25T09:00:00"}}}'

```



Expected results:

\- ✅ Discord: Red critical alert with AI analysis

\- ✅ TheHive: Case created with severity 3

\- ✅ Wazuh: IP block command sent

\- ✅ n8n: Full pipeline green



\---



\## Test 2 — High Alert (SQL Injection)



```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" `

&#x20; -Method POST -ContentType "application/json" `

&#x20; -Body '{"body":{"body":{"rule":{"id":"5712","description":"SQL Injection Attack Detected","level":8},"agent":{"name":"metasploitable"},"data":{"srcip":"192.168.56.102"},"timestamp":"2026-06-25T09:00:00"}}}'

```



Expected results:

\- ✅ Discord: Orange high alert with AI analysis

\- ✅ TheHive: Case created with severity 2

\- ✅ n8n: Full pipeline green



\---



\## Test 3 — Medium Alert (Port Scan)



```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" `

&#x20; -Method POST -ContentType "application/json" `

&#x20; -Body '{"body":{"body":{"rule":{"id":"5713","description":"Port Scan Activity Detected","level":6},"agent":{"name":"metasploitable"},"data":{"srcip":"192.168.56.103"},"timestamp":"2026-06-25T09:00:00"}}}'

```



Expected results:

\- ✅ Discord: Yellow medium alert with AI analysis

\- ✅ TheHive: Case created with severity 1

\- ✅ n8n: Full pipeline green



\---



\## Test 4 — WAF Attack (ModSecurity Block)



```powershell

Invoke-RestMethod -Uri "http://localhost:5678/webhook/wazuh-alert" `

&#x20; -Method POST -ContentType "application/json" `

&#x20; -Body '{"body":{"body":{"rule":{"id":"981176","description":"ModSecurity WAF: SQL Injection Attack Blocked","level":10},"agent":{"name":"modsecurity-waf"},"data":{"srcip":"192.168.56.101"},"timestamp":"2026-06-25T10:00:00"}}}'

```



Expected results:

\- ✅ Discord: Red critical alert

\- ✅ TheHive: WAF case created

\- ✅ Agent shows modsecurity-waf



\---



\## Test 5 — WAF Direct Attack



```bash

\# SQL Injection — expect 403 Forbidden

curl "http://localhost:8080/?id=1' OR '1'='1"



\# XSS Attack — expect 403 Forbidden

curl "http://localhost:8080/?q=<script>alert(1)</script>"



\# Path Traversal — expect 403 Forbidden

curl "http://localhost:8080/?file=../../etc/passwd"

```



\---



\## Test 6 — SSO Login



1\. Open http://localhost in browser

2\. Click SSO login

3\. Redirects to Authentik

4\. Login with akadmin credentials

5\. Redirects back to Wazuh Dashboard

6\. Verify access granted



\---



\## Verify All Services



```powershell

docker ps | findstr "Up"

```



All should show Up:

\- single-node-wazuh.manager-1

\- single-node-wazuh.indexer-1

\- single-node-wazuh.dashboard-1

\- authentik-authentik-server-1

\- authentik-authentik-worker-1

\- authentik-postgresql-1

\- authentik-redis-1

\- thehive

\- n8n

\- modsecurity-waf



\---



\## Full Pipeline Timing



| Step | Expected Time |

|---|--

