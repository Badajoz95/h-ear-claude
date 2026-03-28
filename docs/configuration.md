# Configuration

H-ear MCP Server supports multiple Claude clients and transport modes.

---

## Claude Desktop

Add to your Claude Desktop config file:

**macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
**Windows:** `%LOCALAPPDATA%\Packages\Claude_pzs8sxrjxfjjc\LocalCache\Roaming\Claude\claude_desktop_config.json`

> **Note:** On Windows MSIX installs, the standard `%APPDATA%\Claude\` path does not work. Use the path above.

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

If `npx` is not found (common with nvm), use the full path:

**Windows (nvm):**
```json
{
  "mcpServers": {
    "h-ear": {
      "command": "C:\\nvm4w\\nodejs\\npx.cmd",
      "args": ["-y", "@h-ear/mcp-server"],
      "env": {
        "HEAR_API_KEY": "ncm_sk_your_key_here"
      }
    }
  }
}
```

**macOS (nvm):**
```json
{
  "mcpServers": {
    "h-ear": {
      "command": "/Users/you/.nvm/versions/node/v22.0.0/bin/npx",
      "args": ["-y", "@h-ear/mcp-server"],
      "env": {
        "HEAR_API_KEY": "ncm_sk_your_key_here"
      }
    }
  }
}
```

Restart Claude Desktop after saving the config.

---

## Claude Code

```bash
claude mcp add h-ear -e HEAR_API_KEY=ncm_sk_your_key_here -- npx -y @h-ear/mcp-server
```

Or import from Claude Desktop config:
```bash
claude mcp add-from-claude-desktop
```

Verify:
```bash
claude mcp list
```

---

## claude.ai (Connectors Directory)

H-ear is available as a connector in claude.ai:

1. Go to **Settings > Connectors**
2. Find **H-ear** in the directory
3. Click **Connect** — OAuth login handles authentication automatically

No API key or manual configuration needed.

---

## Remote (Streamable HTTP)

For clients that support remote MCP servers via Streamable HTTP:

```json
{
  "mcpServers": {
    "h-ear": {
      "url": "https://ncm-apim.azure-api.net/h-ear-enterprise-api-prod/mcp",
      "headers": {
        "X-NCM-Api-Key": "ncm_sk_your_key_here"
      }
    }
  }
}
```

**Environments:**

| Environment | Endpoint |
|-------------|----------|
| Production | `https://ncm-apim.azure-api.net/h-ear-enterprise-api-prod/mcp` |
| Staging | `https://ncm-apim.azure-api.net/h-ear-enterprise-api-staging/mcp` |
| Development | `https://ncm-apim.azure-api.net/h-ear-enterprise-api-dev/mcp` |

---

## CLI Options

```
npx @h-ear/mcp-server [options]

Options:
  --key <api-key>     API key (overrides HEAR_API_KEY env var)
  --env <environment> API environment: dev, staging, prod (default: prod)
  --base-url <url>    Override API base URL
```

---

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `HEAR_API_KEY` | API key for authenticated tools | — |
| `HEAR_ENV` | API environment | `prod` |
| `HEAR_BASE_URL` | Override API base URL | Per-environment default |
