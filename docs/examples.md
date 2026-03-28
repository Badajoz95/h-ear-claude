# Examples

Real-world use cases showing what you can ask Claude with H-ear connected.

---

## 1. Noise Monitoring

> "Classify this recording from my balcony to see what noise is affecting my apartment"

Claude will call `classifyAudio` with your file:

```
Tool: classifyAudio
Input: { "filePath": "/Users/you/recordings/balcony-morning.mp3", "threshold": 0.2 }
```

**Example result:**

| Sound | Confidence | Category |
|-------|-----------|----------|
| Traffic noise | 0.87 | Vehicle |
| Car horn | 0.62 | Vehicle |
| Bird | 0.54 | Animal |
| Speech | 0.31 | Human sounds |
| Construction | 0.24 | Tools and machinery |

Claude will summarize: *"Your balcony recording is dominated by traffic noise (87% confidence) with intermittent car horns. There's a natural layer of birdsong and faint speech from neighbours. Construction noise is also present at low levels."*

---

## 2. Wildlife Survey

> "What bird species can you identify in this dawn chorus recording?"

Claude will first check available species, then classify:

```
Tool: listClasses
Input: { "taxonomy": "species", "limit": 5 }

Tool: classifyAudio
Input: { "url": "https://example.com/dawn-chorus.mp3", "threshold": 0.15 }
```

**Example result:**

| Species | Confidence |
|---------|-----------|
| Turdus merula (Common Blackbird) | 0.78 |
| Erithacus rubecula (European Robin) | 0.65 |
| Parus major (Great Tit) | 0.41 |

Claude will provide species names, confidence levels, and context about the habitat.

---

## 3. Batch Processing

> "Classify all the recordings in my noise monitoring folder"

Claude will use batch classification:

```
Tool: classifyBatch
Input: {
  "filePaths": [
    "/recordings/site-a-001.mp3",
    "/recordings/site-a-002.mp3",
    "/recordings/site-b-001.mp3"
  ],
  "threshold": 0.3
}
```

**Example result:**

```
Batch Summary:
  Total files: 3
  Classified: 3
  Failed: 0

  site-a-001.mp3: Traffic noise (0.91), Speech (0.45)
  site-a-002.mp3: Construction (0.88), Impact sounds (0.72)
  site-b-001.mp3: Bird (0.76), Wind (0.52)
```

---

## 4. Sound Library Browsing

> "What categories of sounds can H-ear detect? Show me the animal sounds."

```
Tool: listClasses
Input: { "taxonomy": "audioset-yamnet-521", "category": "Animal" }
```

**Example result:**

| Class | Category |
|-------|----------|
| Dog | Animal |
| Cat | Animal |
| Bird | Animal |
| Insect | Animal |
| Frog | Animal |
| Livestock | Animal |
| ... | Animal |

> "How many total classes are there?"

```
Tool: listClasses
Input: { "taxonomy": "audioset-yamnet-521" }
```

Result: 521 classes across 7 categories: Animal, Human sounds, Music, Natural sounds, Domestic sounds, Vehicle, Tools and machinery.

---

## 5. API Health and Status

> "Is the H-ear API running?"

```
Tool: healthCheck
Input: {}
```

**Response:**
```json
{
  "status": "healthy",
  "version": "1.0.0",
  "deployedTimestamp": "2026-03-28T00:13:13.857Z"
}
```

Claude will confirm: *"H-ear API is healthy, running version 1.0.0, last deployed on March 28, 2026."*

---

## 6. Usage Tracking

> "How many API minutes have I used this month? Am I close to my quota?"

```
Tool: usage
Input: {}
```

**Response:**
```json
{
  "minutesUsed": 142.5,
  "callsToday": 37,
  "quotaRemaining": 857.5,
  "activeKeys": 2
}
```

Claude will summarize: *"You've used 142.5 minutes out of your 1,000-minute quota (14.3%). At today's rate of 37 calls, you have roughly 23 days of capacity remaining."*

---

## 7. Job History Review

> "Show me my last 5 classification jobs"

```
Tool: listJobs
Input: { "limit": 5 }
```

> "Show me the details for job job_abc123"

```
Tool: getJob
Input: { "jobId": "job_abc123" }
```

Claude will display job status, file name, detected events, and timestamps.

---

## Using the classify-audio Prompt

Instead of describing what you want, use the built-in prompt for a guided workflow:

> Use the classify-audio prompt with my recording at https://example.com/audio.mp3

Claude will invoke the `classify-audio` prompt with `detail: "summary"` by default, which orchestrates the full classification and interpretation flow automatically.

For more detail:
> Use classify-audio with detailed analysis on /path/to/file.mp3

This sets `detail: "detailed"` (threshold 0.1) to surface all detected sounds including low-confidence matches.

For temporal analysis:
> Use classify-audio with timestamps on my recording

This sets `detail: "timestamps"` for per-second sound event breakdown.
