---
description: Slack automation week 1 - workspace setup, incoming webhooks, message formatting, and CI/CD notification integration.
---

# Slack Week 1: Basics + Webhooks

## Goal

Master Slack Incoming Webhooks and basic message formatting for CI/CD notifications.

---

## Topics

### Day 1-2: Slack Workspace & Webhooks Setup

**What you'll learn:**
- Slack workspace structure (channels, users, apps)
- Creating a Slack App
- Incoming Webhooks basics
- Webhook URL security

**Hands-on:**
```bash
# 1. Create Slack App
https://api.slack.com/apps → "Create New App"

App Name: Platform Notifications
Workspace: Your workspace

# 2. Enable Incoming Webhooks
Features → Incoming Webhooks → Toggle ON

# 3. Add Webhook to Workspace
"Add New Webhook to Workspace" → Select channel

# 4. Save webhook URL (securely!)
export SLACK_WEBHOOK_URL="https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXX"
```

**Test your webhook:**
```bash
curl -X POST -H 'Content-type: application/json' \
--data '{"text":"Hello from Platform!"}' \
$SLACK_WEBHOOK_URL
```

**Key concepts:**
- **Webhook URL:** Unique URL for posting to a specific channel
- **Incoming Webhooks:** One-way, simple, no OAuth needed
- **Security:** Treat webhook URLs like passwords!

---

### Day 3-4: Message Formatting with Block Kit

**What you'll learn:**
- Basic vs rich message formatting
- Block Kit structure
- Common block types (section, divider, actions)
- Interactive Block Kit Builder

**Simple message (plain text):**
```bash
curl -X POST -H 'Content-type: application/json' \
--data '{
"text": "Pipeline failed: build-service"
}' \
$SLACK_WEBHOOK_URL
```

**Rich message (Block Kit):**
```bash
curl -X POST -H 'Content-type: application/json' \
--data '{
"blocks": [
{
"type": "header",
"text": {
"type": "plain_text",
"text": " Pipeline Failed",
"emoji": true
}
},
{
"type": "section",
"fields": [
{
"type": "mrkdwn",
"text": "*Pipeline:*\nbuild-service-123"
},
{
"type": "mrkdwn",
"text": "*Status:*\nFailed"
},
{
"type": "mrkdwn",
"text": "*Duration:*\n5m 32s"
},
{
"type": "mrkdwn",
"text": "*Author:*\ndemo_user"
}
]
},
{
"type": "divider"
},
{
"type": "context",
"elements": [
{
"type": "mrkdwn",
"text": "Commit: <https://github.com/platform/repo/commit/abc123|abc123> • <https://console.company.com/logs|View Logs>"
}
]
}
]
}' \
$SLACK_WEBHOOK_URL
```

**Block Kit Builder:**
```
Use this interactive tool to design messages:
https://app.slack.com/block-kit-builder/

Copy/paste the JSON output to your webhook call!
```

**Common block types:**

| Block Type | Purpose | Example |
|------------|---------|---------|
| **header** | Bold title | Pipeline Failed |
| **section** | Text + fields | Status, Duration, Author |
| **divider** | Horizontal line | --- |
| **context** | Small text/links | Timestamps, metadata |
| **actions** | Buttons (requires bot) | [Rerun] [View Logs] |

---

### Day 5-6: Channel Routing & Message Threading

**What you'll learn:**
- Channel naming conventions
- Routing messages to different channels
- Using thread_ts for context
- Ephemeral messages (requires bot)

**Channel routing strategy:**
```bash
# Different webhooks for different channels
export SLACK_WEBHOOK_CRITICAL="https://hooks.slack.com/services/.../critical-alerts"
export SLACK_WEBHOOK_INFO="https://hooks.slack.com/services/.../build-notifications"
export SLACK_WEBHOOK_DEPLOYS="https://hooks.slack.com/services/.../deployments"

# Route based on severity
if [ "$SEVERITY" = "critical" ]; then
WEBHOOK=$SLACK_WEBHOOK_CRITICAL
else
WEBHOOK=$SLACK_WEBHOOK_INFO
fi
```

**Channel naming conventions:**
```
#platform-ci-cd - General CI/CD updates
#platform-alerts-critical - P0/P1 alerts only
#platform-builds - Build pipeline status
#platform-deploys - Deployment notifications
#platform-incidents - Incident management
```

**Message threading (note: requires Slack API, not webhooks):**
```python
# You'll learn this in Week 2!
# Webhooks can't create threads, you need the Web API
```

---

### Day 7: Slack Best Practices & Anti-Patterns

**What you'll learn:**
- When to use webhooks vs bots
- Avoiding alert fatigue
- Message design principles
- Security best practices

** DO:**
- Use threads for related updates (requires bot, Week 2)
- Include actionable links (logs, dashboards)
- Use colors/emojis sparingly but effectively
- Aggregate similar alerts
- Different channels for different severities

** DON'T:**
- Post every single event (too noisy!)
- Use @channel / @here for non-critical alerts
- Commit webhook URLs to git
- Send sensitive data (passwords, tokens)
- Use webhooks for two-way interactions (use bot instead)

**Alert fatigue prevention:**
```
Bad:
Pipeline succeeded: build-1
Pipeline succeeded: build-2
Pipeline succeeded: build-3
(Nobody reads these...)

Good:
Hourly Summary: 15/16 pipelines succeeded
1 failure: image-build-service (link)
```

**Security checklist:**
- [ ] Webhook URLs stored in secrets/env vars
- [ ] No sensitive data in messages
- [ ] Rotate webhook URLs periodically
- [ ] Use HTTPS only (always true for Slack)
- [ ] Limit webhook access to specific channels

---

## Exercises

### Exercise 1: Create Your First Webhook

```bash
# 1. Create a test channel
Create: #test-webhooks

# 2. Set up incoming webhook
App → Incoming Webhooks → Add to #test-webhooks

# 3. Send test message
export SLACK_WEBHOOK_URL="your-url-here"

curl -X POST -H 'Content-type: application/json' \
--data '{"text":"My first webhook! "}' \
$SLACK_WEBHOOK_URL

# Expected: Message appears in #test-webhooks
```

---

### Exercise 2: Build a Pipeline Failure Alert

Use Block Kit Builder to create:
```
Header: Pipeline Failed
Fields:
- Pipeline name
- Commit SHA
- Author
- Duration
Link: View Logs (to a fake URL)
```

Test it with curl:
```bash
# Copy JSON from Block Kit Builder
curl -X POST -H 'Content-type: application/json' \
--data '@pipeline-failure.json' \
$SLACK_WEBHOOK_URL
```

---

### Exercise 3: Python Webhook Helper

Create a simple Python script to send formatted messages:

```python
#!/usr/bin/env python3
import requests
import json
import os

SLACK_WEBHOOK = os.environ.get('SLACK_WEBHOOK_URL')

def send_pipeline_alert(pipeline_name, status, duration, author, commit_url):
emoji = "" if status == "success" else ""
color = "good" if status == "success" else "danger"

message = {
"blocks": [
{
"type": "header",
"text": {
"type": "plain_text",
"text": f"{emoji} Pipeline {status.title()}"
}
},
{
"type": "section",
"fields": [
{"type": "mrkdwn", "text": f"*Pipeline:*\n{pipeline_name}"},
{"type": "mrkdwn", "text": f"*Status:*\n{status}"},
{"type": "mrkdwn", "text": f"*Duration:*\n{duration}"},
{"type": "mrkdwn", "text": f"*Author:*\n{author}"}
]
},
{
"type": "context",
"elements": [
{
"type": "mrkdwn",
"text": f"<{commit_url}|View Commit>"
}
]
}
]
}

response = requests.post(SLACK_WEBHOOK, json=message)
return response.status_code == 200

# Test
if __name__ == "__main__":
send_pipeline_alert(
pipeline_name="build-service-123",
status="failed",
duration="5m 32s",
author="demo_user",
commit_url="https://github.com/platform/repo/commit/abc123"
)
```

Save as: `slack_notifier.py`

Test:
```bash
chmod +x slack_notifier.py
export SLACK_WEBHOOK_URL="your-url"
./slack_notifier.py
```

---

### Exercise 4: Tekton Pipeline Integration (Basic)

Create a Tekton Task that sends Slack notifications:

```yaml
apiVersion: tekton.dev/v1
kind: Task
metadata:
name: slack-notify
spec:
params:
- name: message
type: string
description: Message to send
- name: webhook-url
type: string
description: Slack webhook URL
steps:
- name: send-notification
image: curlimages/curl:latest
script: |
#!/bin/sh
curl -X POST -H 'Content-type: application/json' \
--data '{"text":"$(params.message)"}' \
$(params.webhook-url)
```

Use in a pipeline:
```yaml
apiVersion: tekton.dev/v1
kind: Pipeline
metadata:
name: build-with-slack
spec:
params:
- name: slack-webhook
tasks:
- name: build
taskRef:
name: buildah
# ... build params

- name: notify-success
runAfter:
- build
taskRef:
name: slack-notify
params:
- name: message
value: " Build succeeded!"
- name: webhook-url
value: $(params.slack-webhook)
```

---

## Week 1 Checklist

- [ ] Created a Slack App
- [ ] Set up Incoming Webhook
- [ ] Sent test message with curl
- [ ] Built message with Block Kit Builder
- [ ] Created Python helper script
- [ ] Integrated webhook with Tekton (optional)
- [ ] Understand webhook security best practices

---

## Resources

- **Incoming Webhooks:** https://api.slack.com/messaging/webhooks
- **Block Kit Builder:** https://app.slack.com/block-kit-builder/
- **Block Kit Docs:** https://api.slack.com/block-kit
- **Message Formatting:** https://api.slack.com/reference/surfaces/formatting

---

## Real-World Example

Here's a complete example for Platform pipeline notifications:

```python
#!/usr/bin/env python3
"""
Platform Pipeline → Slack Notifier
Trigger this from Tekton Pipeline on completion
"""
import requests
import os
import sys

WEBHOOK_URL = os.environ.get('SLACK_WEBHOOK_URL')

def notify_pipeline_result(pipeline_run_name, status, namespace, logs_url):
"""Send pipeline result to Slack"""

# Emoji and color based on status
if status == "Succeeded":
emoji = ""
status_text = "succeeded"
elif status == "Failed":
emoji = ""
status_text = "failed"
else:
emoji = "⏳"
status_text = status.lower()

# Build message
message = {
"blocks": [
{
"type": "header",
"text": {
"type": "plain_text",
"text": f"{emoji} Pipeline {status_text.title()}",
"emoji": True
}
},
{
"type": "section",
"fields": [
{
"type": "mrkdwn",
"text": f"*Pipeline:*\n{pipeline_run_name}"
},
{
"type": "mrkdwn",
"text": f"*Namespace:*\n{namespace}"
}
]
},
{
"type": "divider"
},
{
"type": "context",
"elements": [
{
"type": "mrkdwn",
"text": f"<{logs_url}|View Logs in Console>"
}
]
}
]
}

# Send to Slack
response = requests.post(WEBHOOK_URL, json=message)

if response.status_code != 200:
print(f"Failed to send Slack notification: {response.status_code}")
sys.exit(1)

print(f"Slack notification sent successfully!")

if __name__ == "__main__":
# Example usage (get these from Tekton params)
notify_pipeline_result(
pipeline_run_name=os.environ.get('PIPELINE_RUN_NAME', 'test-pipeline-run'),
status=os.environ.get('PIPELINE_STATUS', 'Succeeded'),
namespace=os.environ.get('NAMESPACE', 'platform-ci'),
logs_url=os.environ.get('LOGS_URL', 'https://console.company.com/...')
)
```

---

**Next:** [Week 2: Slack API + Bot Development →](../week-02/README.md)
