---
name: google-deep-research
description: Use Google's Deep Research API (Gemini) to conduct comprehensive, multi-source research that runs for 5-20 minutes server-side and produces detailed research reports. Use when the user asks for "deep research", thorough investigation of complex topics, market analysis, competitive landscaping, or any research requiring multiple web sources synthesized into comprehensive reports. This skill uses the Gemini Deep Research Agent API.
---

# Google Deep Research Skill

This skill provides access to Google's Deep Research API, which runs comprehensive research queries server-side for 5-20 minutes, searching multiple web sources and synthesizing findings into detailed reports.

## When to Use This Skill

Use Google Deep Research when:
- User explicitly asks for "deep research" on a topic
- Research requires comprehensive multi-source investigation
- Topic needs 10+ web sources synthesized
- User wants a detailed report (5,000-40,000 chars)
- Standard web searches would be insufficient

Do NOT use for:
- Quick factual lookups (use WebSearch instead)
- Single-source questions
- Time-sensitive queries needing instant answers

## API Configuration

### Endpoint
```
POST https://generativelanguage.googleapis.com/v1beta/interactions
```

### Required Headers
```
Content-Type: application/json
x-goog-api-key: <API_KEY>
```

### Request Payload
```json
{
  "input": "Your detailed research query here",
  "agent": "deep-research-pro-preview-12-2025",
  "background": true
}
```

### Response (Success)
```json
{
  "id": "v1_ChdXXXXXXXXX...",
  "status": "pending"
}
```

## Workflow

### Step 1: Submit Research Query

Use Python to submit the query:

```python
import requests
import json

API_KEY = "<user's API key>"
URL = "https://generativelanguage.googleapis.com/v1beta/interactions"

headers = {
    "Content-Type": "application/json",
    "x-goog-api-key": API_KEY
}

payload = {
    "input": "Research query here - be specific and detailed",
    "agent": "deep-research-pro-preview-12-2025",
    "background": True
}

response = requests.post(URL, headers=headers, json=payload)
data = response.json()
interaction_id = data.get("id")
print(f"Started research: {interaction_id}")
```

### Step 2: Poll for Completion

Research takes 5-20 minutes. Poll the status:

```python
import time

def check_status(interaction_id, api_key):
    url = f"https://generativelanguage.googleapis.com/v1beta/interactions/{interaction_id}"
    headers = {"x-goog-api-key": api_key}

    while True:
        resp = requests.get(url, headers=headers)
        data = resp.json()
        status = data.get("status") or data.get("state")

        if status == "completed":
            # Get the report text
            outputs = data.get("outputs", [])
            if outputs:
                return outputs[-1].get("text", "")
            return None
        elif status in ["failed", "cancelled"]:
            return None
        else:
            print(f"Status: {status} - waiting...")
            time.sleep(30)
```

### Step 3: Save Results

Save the report to a markdown file with metadata:

```python
from datetime import datetime
from pathlib import Path

def save_report(name, report_text, output_dir="research_results"):
    Path(output_dir).mkdir(parents=True, exist_ok=True)
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"{name}_{timestamp}.md"
    filepath = Path(output_dir) / filename

    with open(filepath, "w") as f:
        f.write(f"# Deep Research Report: {name}\n\n")
        f.write(f"**Generated:** {datetime.now().isoformat()}\n\n")
        f.write("---\n\n")
        f.write(report_text)

    return filepath
```

## Query Design Best Practices

### Good Queries

Be specific with names, locations, and what you want:

```
Research solar panel installations at Norwegian shopping centers including
Citycon properties, Steen & Strøm centers, and AMFI centers. For each,
find: building name, address, installation size (kWp), installer company,
and completion year. Search ESG reports, press releases, and sustainability reports.
```

### Bad Queries

Too vague:
```
Tell me about solar panels in Norway
```

### Include in Your Queries

1. **Specific entities** - Company names, building names, locations
2. **Data fields needed** - Address, size, date, installer, etc.
3. **Source hints** - "Search ESG reports, press releases, LinkedIn..."
4. **Geographic scope** - Cities, regions, countries
5. **Time frame** - "2020-2024", "recent", etc.

## Quota Management

### Tier 1 Limits
- ~3 Deep Research queries per API key per day
- Rate limit resets at midnight Pacific Time

### Multi-Key Strategy
If you have multiple API keys, rotate through them:

```python
API_KEYS = [
    "AIzaSy...",  # Key 1
    "AIzaSy...",  # Key 2
    "AIzaSy...",  # Key 3
]

for key in API_KEYS:
    response = submit_query(query, key)
    if response.status_code == 200:
        break
    elif response.status_code == 429:
        continue  # Try next key
```

## Tracking Interactions

Maintain a JSON log to track all research jobs:

```json
{
  "started": "2025-01-01T00:00:00",
  "interactions": [
    {
      "name": "research_topic_name",
      "id": "v1_ChdXXXX...",
      "started_at": "2025-01-01T00:00:00",
      "status": "completed",
      "api_key_used": "...last8chars",
      "output_file": "path/to/report.md",
      "report_length": 25000
    }
  ]
}
```

## Error Handling

### Common Errors

| Status | Meaning | Action |
|--------|---------|--------|
| 400 | Invalid API key | Check key format, regenerate if needed |
| 429 | Quota exceeded | Use different API key or wait 24h |
| 500 | Server error | Retry after 1 minute |

### Invalid API Key Response
```json
{
  "error": {
    "code": 400,
    "message": "API key not valid",
    "status": "INVALID_ARGUMENT"
  }
}
```

## Getting API Keys

1. Go to https://aistudio.google.com/apikey
2. Create a new API key
3. Store securely (environment variable or secure storage)

## Example: Full Research Script

See `~/.claude/skills/google-deep-research/deep_research.py` for a complete implementation.

## Output Format

Deep Research reports typically include:
- Executive summary
- Detailed findings organized by topic
- Source citations with URLs
- Data tables where applicable
- Recommendations or conclusions

Reports range from 5,000 to 40,000 characters depending on topic complexity.
