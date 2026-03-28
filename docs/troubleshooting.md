# Troubleshooting

Common issues and solutions when using H-ear MCP Server.

---

## "No API key provided" Warning

**Symptom:** Server starts but warns that authenticated tools will fail.

**Cause:** `HEAR_API_KEY` environment variable is not set and `--key` flag was not provided.

**Fix:** Set the environment variable or pass the key:
```bash
export HEAR_API_KEY=ncm_sk_your_key_here
npx @h-ear/mcp-server
```

Public tools (`healthCheck`, `listClasses`) still work without a key.

---

## Claude Desktop: "npx not found"

**Symptom:** H-ear tools don't appear in Claude Desktop after config.

**Cause:** Claude Desktop (MSIX on Windows) runs in a sandboxed environment that may not include your shell PATH, especially with nvm/nvm4w.

**Fix:** Use the full path to `npx` in your config:
```json
{
  "command": "C:\\nvm4w\\nodejs\\npx.cmd",
  "args": ["-y", "@h-ear/mcp-server"]
}
```

Find your npx path:
```bash
# Windows
where npx

# macOS/Linux
which npx
```

---

## Claude Desktop: Tools Not Appearing

**Symptom:** Config looks correct but no H-ear tools in Claude Desktop.

**Steps:**
1. Check you're editing the correct config file (see [Configuration](configuration.md))
2. Fully quit Claude Desktop (system tray > Quit), then relaunch
3. Check the MCP server log:

**Windows:**
```
%LOCALAPPDATA%\Packages\Claude_pzs8sxrjxfjjc\LocalCache\Roaming\Claude\logs\mcp-server-h-ear.log
```

**macOS:**
```
~/Library/Logs/Claude/mcp-server-h-ear.log
```

The log shows whether the server started, handshake completed, and tools registered.

---

## Large File Classification Fails

**Symptom:** `classifyAudio` fails on files larger than 25 MB.

**Cause:** ffmpeg is not installed or not in PATH. The server needs ffmpeg to split large files into chunks.

**Fix:** Install ffmpeg:
```bash
# macOS
brew install ffmpeg

# Windows (chocolatey)
choco install ffmpeg

# Ubuntu/Debian
sudo apt install ffmpeg
```

Files under 25 MB do not require ffmpeg.

---

## Error Codes

| HTTP Status | Meaning | Solution |
|-------------|---------|----------|
| 401 | Invalid or missing API key | Check `HEAR_API_KEY` value |
| 403 | Key valid but subscription inactive | Check your H-ear account status |
| 413 | File too large (>25 MB without chunking) | Install ffmpeg for auto-chunking |
| 429 | Rate limit exceeded | Wait and retry; consider upgrading tier |

---

## Rate Limits

| Tier | Calls/Hour | Calls/Day |
|------|-----------|-----------|
| Starter | 100 | 1,000 |
| Pro | 2,000 | 50,000 |

Check your current usage:
> "How many API calls have I made today?"

Claude will call the `usage` tool to show your quota status.

---

## Environment Variable Not Picked Up

**Symptom:** API key is set in your shell but the MCP server doesn't see it.

**Cause:** Claude Desktop and Claude Code may not inherit your shell environment.

**Fix:** Set the key explicitly in the MCP config `env` block rather than relying on shell environment:
```json
{
  "env": {
    "HEAR_API_KEY": "ncm_sk_your_key_here"
  }
}
```

---

## Getting Help

- File an issue: [github.com/Badajoz95/h-ear-claude/issues](https://github.com/Badajoz95/h-ear-claude/issues)
- Email: support@h-ear.world
- Website: [h-ear.world](https://h-ear.world)
