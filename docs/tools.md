# Tool Reference

H-ear MCP Server exposes 7 tools, 1 resource, and 1 prompt via the Model Context Protocol.

---

## Classification Tools

### classifyAudio

Classify a local audio file or public URL for sound detection. Returns detected sound classes with confidence scores.

**Authentication:** Required (API key)

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `filePath` | string | No | — | Path to a local audio file. Recommended. Large files (>25 MB) auto-split via ffmpeg. |
| `url` | string (URL) | No | — | Public URL to an audio file. |
| `threshold` | number (0.0-1.0) | No | 0.3 | Minimum confidence score. Only classes above this are returned. |
| `filterMinDurationSeconds` | number (0.1-595) | No | — | Minimum event duration filter in seconds. |

Provide either `filePath` or `url` (not both).

**Behaviour:**
- Submits the job asynchronously (HTTP 202), then polls until complete
- Files over 25 MB are split into 120-second chunks with 30-second overlap
- Chunked results are merged with timestamp offsets and duplicate event deduplication
- Large file chunking requires `ffmpeg` in your PATH

**Example response:**
```json
{
  "jobId": "job_abc123",
  "status": "completed",
  "classifications": [
    { "class": "Dog", "confidence": 0.92, "category": "Animal" },
    { "class": "Speech", "confidence": 0.45, "category": "Human sounds" }
  ],
  "noiseEvents": [
    { "class": "Dog", "startTime": 2.1, "endTime": 8.4, "confidence": 0.92 }
  ]
}
```

---

### classifyBatch

Classify multiple audio files in one operation. Supports up to 50 files.

**Authentication:** Required (API key)

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `filePaths` | string[] (1-50) | No | — | Array of local audio file paths. Recommended. |
| `files` | array of {url, id?} (1-50) | No | — | Array of public audio URLs. Requires `callbackUrl`. |
| `callbackUrl` | string (URL, HTTPS) | No | — | Webhook URL for async results. Required with URL mode. |
| `callbackSecret` | string | No | — | HMAC-SHA256 secret for webhook signature verification. |
| `threshold` | number (0.0-1.0) | No | 0.3 | Confidence threshold applied to all files. |
| `filterMinDurationSeconds` | number (0.1-595) | No | — | Minimum event duration filter. |

**Two modes:**
- **Local mode** (`filePaths`): Submits all files async, then polls each for results
- **URL mode** (`files`): Calls batch API endpoint with webhook callback

**Example response:**
```json
{
  "mode": "local",
  "totalFiles": 3,
  "classified": 3,
  "failed": 0,
  "results": [
    { "file": "recording-1.mp3", "status": "completed", "classifications": [...] },
    { "file": "recording-2.mp3", "status": "completed", "classifications": [...] },
    { "file": "recording-3.mp3", "status": "completed", "classifications": [...] }
  ]
}
```

---

## Discovery Tools

### listClasses

Browse supported audio classification classes across 3 taxonomies.

**Authentication:** None required

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `taxonomy` | enum | No | `audioset-yamnet-521` | Taxonomy to browse. |
| `category` | string | No | — | Filter by category (case-insensitive partial match). |
| `limit` | integer > 0 | No | — | Max classes to return. |
| `offset` | integer >= 0 | No | — | Number of classes to skip (pagination). |

**Taxonomies:**

| ID | Classes | Description |
|----|---------|-------------|
| `audioset-yamnet-521` | 521 | General sound classification across 7 categories (default) |
| `audioset-panns-527` | 527 | Extended AudioSet classification |
| `species` | 6,522 | Bird species identification |

**Categories** (audioset-yamnet-521): Animal, Human sounds, Music, Natural sounds, Domestic sounds, Vehicle, Tools and machinery.

---

### healthCheck

Check API health and liveness. Returns status, version, and deployment timestamp.

**Authentication:** None required

**Parameters:** None

**Example response:**
```json
{
  "status": "healthy",
  "version": "1.0.0",
  "deployedTimestamp": "2026-03-28T00:13:13.857Z"
}
```

---

## Account Tools

### usage

Get API usage statistics for your account.

**Authentication:** Required (API key)

**Parameters:** None

**Example response:**
```json
{
  "minutesUsed": 142.5,
  "callsToday": 37,
  "quotaRemaining": 857.5,
  "activeKeys": 2
}
```

---

### listJobs

List recent classification jobs with status and metadata.

**Authentication:** Required (API key)

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `limit` | integer (1-100) | No | 10 | Max jobs to return. |
| `offset` | integer >= 0 | No | 0 | Number of jobs to skip. |
| `status` | enum | No | — | Filter: `processing`, `completed`, or `failed`. |

---

### getJob

Get detailed results for a specific classification job.

**Authentication:** Required (API key)

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | string | Yes | Job ID returned from `classifyAudio` or `classifyBatch`. |

---

## Resource

### h-ear://status

Live API status including health, version, environment, available taxonomies, and class counts.

**URI:** `h-ear://status`
**MIME type:** `application/json`
**Authentication:** None required

Returns merged data from health check and class listing endpoints.

---

## Prompt

### classify-audio

Pre-built prompt that guides Claude through classifying audio and interpreting results.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `audioSource` | string | Yes | — | URL or description of the audio to classify. |
| `detail` | enum | No | `summary` | Detail level for the analysis. |

**Detail levels:**

| Level | Threshold | Behaviour |
|-------|-----------|-----------|
| `summary` | 0.3 | Top sound classes with confidence levels |
| `detailed` | 0.1 | All detected classes including low-confidence matches |
| `timestamps` | 0.3 | Per-second breakdown of sound events with timing |

The prompt instructs Claude to:
1. Call `classifyAudio` with the appropriate settings
2. Summarize dominant sounds with confidence levels
3. Identify the sound environment (urban, natural, industrial, etc.)
4. Flag notable or unusual sounds
