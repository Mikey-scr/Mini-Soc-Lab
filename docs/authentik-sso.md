\# 🔐 Authentik SSO Integration



\## Overview

Authentik acts as the Identity Provider (IdP) for the Mini SOC Lab.

It provides Single Sign-On (SSO) via OpenID Connect (OIDC) for the

Wazuh Dashboard and Role-Based Access Control (RBAC).



\---



\## Architecture



```

Browser → Wazuh Dashboard → Authentik (OIDC) → Role Mapping → Access Granted

```



\---



\## Setup Steps



\### 1. Create Authentik Application

\- Login to Authentik Admin: http://localhost:9000

\- Navigate to Applications → Applications

\- Click Create with Provider

\- Name: SOC-DASH

\- Slug: soc-dash

\- Provider Type: OAuth2/OpenID



\### 2. Configure Provider

\- Name: Wazuh Provider

\- Authorization flow: default-provider-authorization-implicit-consent

\- Client Type: Confidential

\- Redirect URIs: http://192.168.56.107/auth/openid/login

\- Scopes: openid, profile, email



\### 3. Create User Group

\- Navigate to Directory → Groups

\- Create group: authentik-users

\- Add admin user to group



\### 4. Configure Wazuh Dashboard

Add to opensearch\_dashboards.yml:

```yaml

opensearch\_security.auth.type: "openid"

opensearch\_security.openid.connect\_url: "http://192.168.56.107:9000/application/o/soc-dash/.well-known/openid-configuration"

opensearch\_security.openid.client\_id: "YOUR\_CLIENT\_ID"

opensearch\_security.openid.client\_secret: "YOUR\_CLIENT\_SECRET"

opensearch\_security.openid.base\_redirect\_url: "http://192.168.56.107"

opensearch\_security.openid.scope: "openid profile email"

opensearch\_security.openid.verify\_hostnames: false

```



\### 5. Configure Wazuh Indexer

Add OpenID domain to config.yml:

```yaml

openid\_auth\_domain:

&#x20; http\_enabled: true

&#x20; transport\_enabled: false

&#x20; order: 1

&#x20; http\_authenticator:

&#x20;   type: openid

&#x20;   challenge: false

&#x20;   config:

&#x20;     subject\_key: preferred\_username

&#x20;     roles\_key: groups

&#x20;     openid\_connect\_url: "http://192.168.56.107:9000/application/o/soc-dash/.well-known/openid-configuration"

&#x20; authentication\_backend:

&#x20;   type: noop

```



\### 6. Map Roles in Wazuh Indexer

```bash

curl -X PUT "https://localhost:9200/\_plugins/\_security/api/rolesmapping/all\_access" \\

&#x20; --cert /usr/share/wazuh-indexer/certs/admin.pem \\

&#x20; --key /usr/share/wazuh-indexer/certs/admin-key.pem \\

&#x20; -k -H "Content-Type: application/json" \\

&#x20; -d '{"backend\_roles":\["authentik-users"],"users":\["akadmin"]}'

```



\---



\## Troubleshooting



| Error | Fix |

|---|---|

| 401 Unauthorized | Check role mapping in indexer |

| 404 on connect\_url | Verify Authentik slug is correct |

| Redirect loop | Check base\_redirect\_url matches actual URL |

| ERR\_CONNECTION\_REFUSED | Use IP instead of hostname |



\---



\## Key Files

\- `config/wazuh\_dashboard/opensearch\_dashboards.yml`

\- `docker/authentik/docker-compose.yml`

