# H-ear MCP Server

AI-powered sound classification for Claude. Identify sounds, detect noise events, and classify audio across 7,000+ sound classes — from urban noise to bird species.

H-ear connects Claude to the [H-ear World](https://h-ear.world) audio classification API via the [Model Context Protocol](https://modelcontextprotocol.io).

## Quick Start

```bash
npx @h-ear/mcp-server --key YOUR_API_KEY
```

That's it. Claude now has 7 audio classification tools, a live status resource, and a guided classification prompt.

## Tools

| Tool | Auth | Description |
|------|------|-------------|
| `classifyAudio` | Key | Classify audio from a file or URL. Async submit + poll. Large files auto-chunked. |
| `classifyBatch` | Key | Batch classify up to 50 files at once. |
| `listClasses` | None | Browse 7,000+ sound classes across 3 taxonomies. |
| `healthCheck` | None | API liveness check — status, version, deployment timestamp. |
| `usage` | Key | API quota: minutes used, calls today, remaining balance. |
| `listJobs` | Key | Recent classification job history with pagination. |
| `getJob` | Key | Detailed job result with all detected sound events. |

Plus:
- **Resource** `h-ear://status` — live API status with taxonomies and class counts
- **Prompt** `classify-audio` — guided workflow with configurable detail level

## Supported Audio

| Formats | Max Size | Max Duration |
|---------|----------|-------------|
| MP3, WAV, FLAC, OGG, M4A | 25 MB (auto-chunked above) | 10 minutes |

Files over 25 MB are automatically split into overlapping chunks using ffmpeg, classified in parallel, then merged with deduplication.

## Taxonomies

| Taxonomy | Classes | Use Case |
|----------|---------|----------|
| `audioset-yamnet-521` | 521 | General sound classification (default) |
| `audioset-panns-527` | 527 | Extended general classification |
| `species` | 6,522 | Bird species identification |

## Authentication

Get an API key from [h-ear.world](https://h-ear.world) (Enterprise plan). Pass it via:
- Environment variable: `HEAR_API_KEY`
- CLI flag: `--key YOUR_KEY`
- OAuth 2.0 (automatic via claude.ai Connectors Directory)

Two tools (`healthCheck`, `listClasses`) work without authentication.

## Transport

| Transport | Endpoint |
|-----------|----------|
| stdio (npm) | `npx @h-ear/mcp-server --key YOUR_KEY` |
| Streamable HTTP | `https://ncm-apim.azure-api.net/h-ear-enterprise-api-prod/mcp` |

## Documentation

- [Tool Reference](docs/tools.md) — all 7 tools with schemas and parameters
- [Examples](docs/examples.md) — real-world use cases with prompts
- [Authentication](docs/authentication.md) — API keys, OAuth, Enterprise tiers
- [Configuration](docs/configuration.md) — Claude Desktop, Claude Code, claude.ai setup
- [Troubleshooting](docs/troubleshooting.md) — common issues and solutions

## Links

- [npm: @h-ear/mcp-server](https://www.npmjs.com/package/@h-ear/mcp-server)
- [npm: @h-ear/core](https://www.npmjs.com/package/@h-ear/core)
- [H-ear World](https://h-ear.world)
- [OpenClaw Skill](https://github.com/Badajoz95/h-ear-openclaw) (WhatsApp, Telegram, Slack, Discord, Teams)

## License

MIT
