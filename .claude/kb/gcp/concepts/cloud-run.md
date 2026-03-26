# Cloud Run

> **Purpose**: Fully managed serverless container platform for HTTP services and event-driven workloads
> **Confidence**: 0.95
> **MCP Validated**: 2026-02-17

## Overview

Cloud Run is a fully managed compute platform that automatically scales stateless containers.
It supports both HTTP request-driven services and event-driven jobs triggered by Pub/Sub,
Cloud Scheduler, or Eventarc. Each container instance can handle multiple concurrent requests,
and billing is based on actual CPU and memory usage during request processing.

## The Pattern

```python
import os
import json
import base64
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/", methods=["POST"])
def handle_pubsub():
    """Cloud Run service triggered by Pub/Sub push subscription."""
    envelope = request.get_json()
    if not envelope or "message" not in envelope:
        return jsonify(error="invalid Pub/Sub message"), 400

    message = envelope["message"]
    data = json.loads(base64.b64decode(message["data"]).decode("utf-8"))
    attributes = message.get("attributes", {})

    # Process the event
    bucket = attributes.get("bucketId")
    filename = attributes.get("objectId")

    print(f"Processing file: gs://{bucket}/{filename}")
    # ... your processing logic here ...

    return jsonify(status="ok"), 200

if __name__ == "__main__":
    port = int(os.environ.get("PORT", 8080))
    app.run(host="0.0.0.0", port=port)
```

## Quick Reference

| Input | Output | Notes |
|-------|--------|-------|
| Pub/Sub push message | HTTP 200/204 | Must return 2xx to acknowledge |
| HTTP GET/POST request | JSON response | Standard REST endpoint |
| Cloud Scheduler cron | Trigger execution | Via Pub/Sub or HTTP |
| Container start | Listening on `$PORT` | Must bind to 0.0.0.0 |

## Common Mistakes

### Wrong

```python
# Hardcoding port and binding to localhost
if __name__ == "__main__":
    app.run(host="127.0.0.1", port=5000)
```

### Correct

```python
# Reading PORT from environment, binding to all interfaces
if __name__ == "__main__":
    port = int(os.environ.get("PORT", 8080))
    app.run(host="0.0.0.0", port=port)
```

## Dockerfile Pattern

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["gunicorn", "--bind", "0.0.0.0:8080", "--workers", "1", \
     "--threads", "8", "--timeout", "0", "main:app"]
```

## Key Configuration

| Setting | Default | Recommended |
|---------|---------|-------------|
| `--memory` | 512Mi | 512Mi-2Gi for data pipelines |
| `--cpu` | 1 | 1-2 for CPU-bound work |
| `--concurrency` | 80 | 1 for heavy processing |
| `--timeout` | 300s | Up to 3600s for long jobs |
| `--min-instances` | 0 | 1+ for low-latency services |
| `--max-instances` | 100 | Set based on downstream limits |

## Related

- [Pub/Sub](../concepts/pubsub.md) - Message trigger source
- [IAM](../concepts/iam.md) - Service account configuration
- [Cloud Run Scaling](../patterns/cloud-run-scaling.md) - Scaling patterns
- [Event-Driven Pipeline](../patterns/event-driven-pipeline.md) - End-to-end pipeline
