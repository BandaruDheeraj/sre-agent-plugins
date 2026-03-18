# NeuBird Hawkeye MCP Server Plugin

Connects Azure SRE Agent to [NeuBird Hawkeye](https://neubird.ai), an AI-powered autonomous incident investigation platform that provides root cause analysis, alert management, and investigation capabilities via the Hawkeye MCP server.

## Endpoint

Each Hawkeye deployment has a unique URL based on your organization's deployment name:

```
https://<your-deployment-name>.app.neubird.ai/mcp
```

Replace `<your-deployment-name>` with your organization's Hawkeye deployment name (e.g., `acme-corp`).

## Authentication

Hawkeye supports two authentication methods. Choose the one that best fits your use case.

### Option 1 — Email/Password headers (simplest)

1. Log in to your [NeuBird Hawkeye account](https://neubird.ai)
2. Your account email and password are used directly as authentication headers
3. Set the `X-Hawkeye-Email` header to your account email
4. Set the `X-Hawkeye-Password` header to your account password

**Header format:**

| Header | Value |
|--------|-------|
| `X-Hawkeye-Email` | Your Hawkeye account email |
| `X-Hawkeye-Password` | Your Hawkeye account password |

### Option 2 — Bearer token (for automated pipelines)

1. Obtain a bearer token by calling the Hawkeye login API:

   ```bash
   curl -s -X POST "https://<your-deployment-name>.app.neubird.ai/api/v1/user/login" \
     -H "Content-Type: application/json" \
     -d '{"email": "your@email.com", "password": "your-password"}' \
     | jq -r '.access_token'
   ```

2. The returned `access_token` is a JWT
3. Use it as the `Authorization` header value with the `Bearer` prefix
4. Tokens auto-refresh on subsequent requests

**Header format:**

| Header | Value |
|--------|-------|
| `Authorization` | `Bearer <your-token>` |

> [!TIP]
> For production use, create a dedicated **service account** in Hawkeye rather than using personal credentials. This ensures the integration remains functional even if a team member leaves the organization.

## Prerequisites

- An active NeuBird Hawkeye account with at least one connected data source
- Hawkeye supports connections to AWS, Azure, GCP, Datadog, PagerDuty, New Relic, Grafana, and other monitoring platforms
- At least one project configured with connected data sources

## Add the connector in Azure portal

1. Navigate to your SRE Agent resource
2. Select **Builder** > **Connectors** > **Add connector**
3. Select **NeuBird Hawkeye** and select **Next**
4. Configure the connector:

   **For Email/Password authentication:**

   | Field | Value |
   |-------|-------|
   | **Name** | `hawkeye` |
   | **Connection type** | Streamable-HTTP (pre-selected) |
   | **URL** | `https://<your-deployment-name>.app.neubird.ai/mcp` |
   | **X-Hawkeye-Email** | Your Hawkeye account email |
   | **X-Hawkeye-Password** | Your Hawkeye account password |

   **For Bearer token authentication:**

   | Field | Value |
   |-------|-------|
   | **Name** | `hawkeye` |
   | **Connection type** | Streamable-HTTP (pre-selected) |
   | **URL** | `https://<your-deployment-name>.app.neubird.ai/mcp` |
   | **Authorization** | `Bearer <your-token>` |

5. Select **Next** to review, then **Add connector**

> [!IMPORTANT]
> Keep your credentials secure. If using email/password headers, ensure the account has a strong, unique password. If using a bearer token, store it securely and rotate it regularly.

## Available capabilities

Once connected, your SRE Agent can:

- **Investigate alerts autonomously** — Trigger AI-powered investigations that correlate data across all connected sources
- **Get root cause analysis** — Retrieve detailed RCAs with timelines, corrective actions, and business impact assessments
- **Manage investigations** — List uninvestigated alerts, monitor investigation progress, and ask follow-up questions
- **Create manual investigations** — Start investigations from text descriptions without requiring an alert ID
- **Generate incident reports** — Access organization-wide analytics including MTTR, time saved, and investigation quality
- **Manage projects and connections** — Create projects, configure data source connections, and organize investigations
