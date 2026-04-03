# Authentication

H-ear uses API keys for direct access and OAuth 2.1 + PKCE for the Claude Connectors Directory.

---

## Which Tools Need Auth?

| Tool | Authentication |
|------|---------------|
| `healthCheck` | None |
| `listClasses` | None |
| `classifyAudio` | API Key |
| `classifyBatch` | API Key |
| `usage` | API Key |
| `listJobs` | API Key |
| `getJob` | API Key |
| `createWebhook` | Bearer token (enterprise subscription) |
| `listWebhooks` | Bearer token (enterprise subscription) |
| `getWebhook` | Bearer token (enterprise subscription) |
| `updateWebhook` | Bearer token (enterprise subscription) |
| `deleteWebhook` | Bearer token (enterprise subscription) |
| `pingWebhook` | Bearer token (enterprise subscription) |
| `listWebhookDeliveries` | Bearer token (enterprise subscription) |
| Resource `h-ear://status` | None |

---

## API Key

### Getting a Key

Sign up at [h-ear.world](https://h-ear.world) and subscribe to an Enterprise plan. Your API key is generated in the dashboard.

API keys use the format `ncm_sk_` followed by a 32-character hex string.

### Providing the Key

**Environment variable** (recommended):
```bash
export HEAR_API_KEY=ncm_sk_your_key_here
npx @h-ear/mcp-server
```

**CLI flag:**
```bash
npx @h-ear/mcp-server --key ncm_sk_your_key_here
```

**Claude Desktop config:**
```json
{
  "mcpServers": {
    "h-ear": {
      "command": "npx",
      "args": ["-y", "@h-ear/mcp-server"],
      "env": {
        "HEAR_API_KEY": "ncm_sk_your_key_here"
      }
    }
  }
}
```

---

## OAuth 2.1 + PKCE

When using H-ear through the **Claude Connectors Directory** (claude.ai) or via `claude mcp add --transport http`, authentication is handled automatically via OAuth 2.1 + PKCE.

- **Flow:** Authorization Code + PKCE (RFC 7636)
- **Provider:** Auth0 (`auth.h-ear.world`)
- **Discovery:** RFC 9728 Protected Resource Metadata — Claude auto-discovers the Auth0 endpoints
- **Token lifetime:** 1 hour (Claude manages refresh automatically)
- **Scopes:** `openid profile email`

No manual key configuration is needed — Claude handles the full OAuth flow. On first use, a browser window opens for Auth0 login.

---

## Enterprise Tiers

| Tier | Rate Limit | Daily Quota | Key Prefix |
|------|-----------|-------------|------------|
| **Starter** | 100 calls/hour | 1,000/day | `ncm_sk_` |
| **Pro** | 2,000 calls/hour | 50,000/day | `ncm_sk_` |

Rate limit headers are returned on every response:
- `X-RateLimit-Tier`
- `X-RateLimit-Daily-Limit`
- `X-RateLimit-Hourly-Limit`

---

## Security

- API keys are validated server-side against a hashed key store
- Keys can be regenerated or revoked from the H-ear dashboard
- All API traffic is encrypted via TLS 1.2+
- The APIM gateway enforces IP-based rate limiting (300 calls/60s) before per-key auth
