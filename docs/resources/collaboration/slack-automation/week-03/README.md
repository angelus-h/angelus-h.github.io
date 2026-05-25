---
description: Slack workflow optimization week 3 - alert aggregation, noise reduction, advanced patterns, GitOps integration, and production deployment.
---

# Slack Week 3: Workflow Optimization & Advanced Patterns

## Goal

Optimize Slack workflows to reduce noise, improve signal, and build production-ready integrations.

---

## Topics

### Day 1-2: Alert Aggregation & Noise Reduction

**What you'll learn:**
- Alert batching strategies
- Deduplication patterns
- Time-based aggregation
- Smart throttling

**Problem: Alert Fatigue**
```
Bad approach (too noisy):
 Test failed: unit-test-1
 Test failed: unit-test-2
 Test failed: unit-test-3
 (150 messages in 5 minutes...)

Result: Team ignores all alerts
```

**Solution 1: Time-based batching**
```python
from collections import defaultdict
from datetime import datetime, timedelta
import threading
import time

class AlertAggregator:
 def __init__(self, slack_client, batch_window_seconds=300):
 self.client = slack_client
 self.batch_window = batch_window_seconds
 self.pending_alerts = defaultdict(list)
 self.lock = threading.Lock()

 # Start background thread to flush batches
 self.start_flush_timer()

 def add_alert(self, category, alert_data):
 """Add alert to batch"""
 with self.lock:
 self.pending_alerts[category].append(alert_data)

 def flush_alerts(self):
 """Send batched alerts to Slack"""
 with self.lock:
 if not self.pending_alerts:
 return

 for category, alerts in self.pending_alerts.items():
 self._send_batch(category, alerts)

 # Clear pending
 self.pending_alerts.clear()

 def _send_batch(self, category, alerts):
 """Send aggregated alert to Slack"""
 count = len(alerts)

 if count == 1:
 # Single alert, send normally
 alert = alerts[0]
 self.client.chat_postMessage(
 channel="#platform-alerts",
 text=f" {category}: {alert['name']}"
 )
 else:
 # Multiple alerts, send summary
 alert_list = "\n".join([f"• {a['name']}" for a in alerts[:10]])
 more = f"\n...and {count - 10} more" if count > 10 else ""

 self.client.chat_postMessage(
 channel="#platform-alerts",
 blocks=[
 {
 "type": "header",
 "text": {
 "type": "plain_text",
 "text": f" {count} {category} Failures"
 }
 },
 {
 "type": "section",
 "text": {
 "type": "mrkdwn",
 "text": alert_list + more
 }
 }
 ]
 )

 def start_flush_timer(self):
 """Flush alerts periodically"""
 def timer():
 while True:
 time.sleep(self.batch_window)
 self.flush_alerts()

 t = threading.Thread(target=timer, daemon=True)
 t.start()

# Usage
aggregator = AlertAggregator(slack_client, batch_window_seconds=300) # 5 min

# Add alerts as they come in
aggregator.add_alert("Test Failures", {"name": "unit-test-1"})
aggregator.add_alert("Test Failures", {"name": "unit-test-2"})
aggregator.add_alert("Build Failures", {"name": "image-build-3"})

# Alerts are automatically flushed every 5 minutes
```

**Solution 2: Deduplication**
```python
class AlertDeduplicator:
 def __init__(self, slack_client, dedup_window_seconds=3600):
 self.client = slack_client
 self.dedup_window = dedup_window_seconds
 self.sent_alerts = {} # alert_key -> timestamp

 def send_alert(self, alert_key, message):
 """Send alert only if not recently sent"""
 now = time.time()

 # Check if already sent recently
 if alert_key in self.sent_alerts:
 last_sent = self.sent_alerts[alert_key]
 if now - last_sent < self.dedup_window:
 print(f"Alert {alert_key} suppressed (dedup)")
 return False

 # Send alert
 self.client.chat_postMessage(
 channel="#platform-alerts",
 text=message
 )

 # Record sent time
 self.sent_alerts[alert_key] = now
 return True

# Usage
dedup = AlertDeduplicator(slack_client, dedup_window_seconds=3600) # 1 hour

# Same alert within 1 hour = suppressed
dedup.send_alert("pipeline:build-service:failed", " build-service failed")
dedup.send_alert("pipeline:build-service:failed", " build-service failed") # Suppressed!
```

**Solution 3: Smart throttling by severity**
```python
class SmartThrottler:
 """Different throttle rates for different severities"""

 THROTTLE_WINDOWS = {
 "critical": 0, # No throttling
 "high": 300, # 5 minutes
 "medium": 1800, # 30 minutes
 "low": 3600, # 1 hour
 }

 def __init__(self, slack_client):
 self.client = slack_client
 self.last_sent = defaultdict(lambda: 0)

 def send_alert(self, alert_id, severity, message, channel):
 """Send alert with severity-based throttling"""
 now = time.time()
 throttle_window = self.THROTTLE_WINDOWS.get(severity, 300)

 key = f"{alert_id}:{severity}"

 if now - self.last_sent[key] < throttle_window:
 return False # Throttled

 # Send to appropriate channel based on severity
 if severity == "critical":
 channel = "#platform-critical"
 elif severity in ["high", "medium"]:
 channel = "#platform-alerts"
 else:
 channel = "#platform-info"

 self.client.chat_postMessage(
 channel=channel,
 text=message
 )

 self.last_sent[key] = now
 return True
```

---

### Day 3-4: Intelligent Routing & Context

**What you'll learn:**
- Severity-based channel routing
- Adding rich context to alerts
- Dynamic channel creation
- Alert enrichment

**Channel routing strategy:**
```python
class AlertRouter:
 """Route alerts to appropriate channels based on context"""

 CHANNEL_MAP = {
 # Severity routing
 "critical": "#platform-critical",
 "high": "#platform-alerts",
 "medium": "#platform-alerts",
 "low": "#platform-info",

 # Component routing
 "pipeline": "#platform-pipelines",
 "build": "#platform-builds",
 "deploy": "#platform-deploys",
 "security": "#platform-security",
 }

 def __init__(self, slack_client):
 self.client = slack_client

 def route_alert(self, alert):
 """Determine channel(s) for alert"""
 channels = set()

 # Route by severity
 if "severity" in alert:
 channels.add(self.CHANNEL_MAP.get(alert["severity"], "#platform-alerts"))

 # Route by component
 if "component" in alert:
 channels.add(self.CHANNEL_MAP.get(alert["component"], "#platform-alerts"))

 return list(channels)

 def send_alert(self, alert):
 """Send alert to appropriate channel(s)"""
 channels = self.route_alert(alert)

 for channel in channels:
 self.client.chat_postMessage(
 channel=channel,
 blocks=self._build_alert_message(alert)
 )

 def _build_alert_message(self, alert):
 """Build rich alert message"""
 emoji_map = {
 "critical": "",
 "high": "",
 "medium": "",
 "low": ""
 }

 emoji = emoji_map.get(alert.get("severity", "medium"), "")

 return [
 {
 "type": "header",
 "text": {
 "type": "plain_text",
 "text": f"{emoji} {alert['title']}"
 }
 },
 {
 "type": "section",
 "fields": [
 {"type": "mrkdwn", "text": f"*Severity:*\n{alert['severity']}"},
 {"type": "mrkdwn", "text": f"*Component:*\n{alert.get('component', 'N/A')}"},
 {"type": "mrkdwn", "text": f"*Environment:*\n{alert.get('environment', 'production')}"},
 {"type": "mrkdwn", "text": f"*Time:*\n{alert.get('timestamp', 'now')}"}
 ]
 },
 {
 "type": "section",
 "text": {"type": "mrkdwn", "text": alert.get("description", "")}
 },
 {
 "type": "actions",
 "elements": [
 {
 "type": "button",
 "text": {"type": "plain_text", "text": "View Dashboard"},
 "url": alert.get("dashboard_url", "#")
 },
 {
 "type": "button",
 "text": {"type": "plain_text", "text": "View Logs"},
 "url": alert.get("logs_url", "#")
 },
 {
 "type": "button",
 "text": {"type": "plain_text", "text": "Acknowledge"},
 "action_id": "ack_alert",
 "value": alert.get("id", "")
 }
 ]
 }
 ]

# Usage
router = AlertRouter(slack_client)

alert = {
 "id": "alert-123",
 "title": "High Error Rate Detected",
 "severity": "critical",
 "component": "pipeline",
 "environment": "production",
 "description": "Error rate spiked to 25% in the last 5 minutes",
 "dashboard_url": "https://grafana.example.com/d/pipeline-errors",
 "logs_url": "https://console.company.com/logs?filter=errors"
}

router.send_alert(alert)
# Sends to both #platform-critical and #platform-pipelines
```

**Context enrichment:**
```python
def enrich_pipeline_alert(pipeline_run):
 """Add context to pipeline alerts"""
 import requests

 # Get pipeline metadata
 namespace = pipeline_run['metadata']['namespace']
 name = pipeline_run['metadata']['name']

 # Fetch related context
 # 1. Git commit info
 commit_sha = pipeline_run.get('spec', {}).get('params', {}).get('revision', 'unknown')

 # 2. Recent failure rate
 # (Query Prometheus or Tekton API)
 failure_rate = get_recent_failure_rate(namespace)

 # 3. Similar recent failures
 recent_failures = find_similar_failures(name, hours=24)

 # 4. On-call engineer
 oncall = get_oncall_engineer()

 return {
 "pipeline": name,
 "commit": commit_sha,
 "failure_rate": f"{failure_rate}%",
 "recent_similar_failures": len(recent_failures),
 "oncall": oncall,
 "context": f"This is the {len(recent_failures) + 1}th failure in 24h"
 }
```

---

### Day 5-6: CI/CD Integration Patterns

**What you'll learn:**
- Tekton → Slack integration
- GitHub/GitLab webhook → Slack
- Deployment notifications
- Rollback workflows

**Tekton Pipeline → Slack (complete flow):**

```yaml
# tekton/tasks/slack-notify.yaml
apiVersion: tekton.dev/v1
kind: Task
metadata:
 name: slack-notify
spec:
 params:
 - name: webhook-url
 type: string
 - name: pipeline-name
 type: string
 - name: status
 type: string
 - name: namespace
 type: string
 - name: logs-url
 type: string
 default: ""
 steps:
 - name: send-notification
 image: python:3.11-slim
 env:
 - name: SLACK_WEBHOOK
 value: $(params.webhook-url)
 script: |
 #!/usr/bin/env python3
 import json
 import urllib.request

 webhook = "$(params.webhook-url)"
 pipeline = "$(params.pipeline-name)"
 status = "$(params.status)"
 namespace = "$(params.namespace)"
 logs_url = "$(params.logs-url)"

 emoji = "" if status == "Succeeded" else ""

 message = {
 "blocks": [
 {
 "type": "header",
 "text": {
 "type": "plain_text",
 "text": f"{emoji} Pipeline {status}"
 }
 },
 {
 "type": "section",
 "fields": [
 {"type": "mrkdwn", "text": f"*Pipeline:*\n{pipeline}"},
 {"type": "mrkdwn", "text": f"*Namespace:*\n{namespace}"}
 ]
 }
 ]
 }

 if logs_url:
 message["blocks"].append({
 "type": "context",
 "elements": [
 {
 "type": "mrkdwn",
 "text": f"<{logs_url}|View Logs>"
 }
 ]
 })

 req = urllib.request.Request(
 webhook,
 data=json.dumps(message).encode('utf-8'),
 headers={'Content-Type': 'application/json'}
 )

 with urllib.request.urlopen(req) as response:
 print(f"Sent: {response.status}")
```

```yaml
# tekton/pipelines/build-with-notifications.yaml
apiVersion: tekton.dev/v1
kind: Pipeline
metadata:
 name: build-with-slack
spec:
 params:
 - name: slack-webhook
 type: string
 workspaces:
 - name: source
 tasks:
 # Main build task
 - name: build
 taskRef:
 name: buildah
 workspaces:
 - name: source
 workspace: source

 # Notify on success
 - name: notify-success
 runAfter:
 - build
 when:
 - input: "$(tasks.build.status)"
 operator: in
 values: ["Succeeded"]
 taskRef:
 name: slack-notify
 params:
 - name: webhook-url
 value: $(params.slack-webhook)
 - name: pipeline-name
 value: "$(context.pipelineRun.name)"
 - name: status
 value: "Succeeded"
 - name: namespace
 value: "$(context.pipelineRun.namespace)"

 # Notify on failure
 - name: notify-failure
 runAfter:
 - build
 when:
 - input: "$(tasks.build.status)"
 operator: in
 values: ["Failed"]
 taskRef:
 name: slack-notify
 params:
 - name: webhook-url
 value: $(params.slack-webhook)
 - name: pipeline-name
 value: "$(context.pipelineRun.name)"
 - name: status
 value: "Failed"
 - name: namespace
 value: "$(context.pipelineRun.namespace)"
```

**GitHub Webhook → Slack:**
```python
from flask import Flask, request
from slack_sdk import WebClient
import hmac
import hashlib

app = Flask(__name__)
slack_client = WebClient(token=os.environ["SLACK_BOT_TOKEN"])

@app.route("/github-webhook", methods=["POST"])
def github_webhook():
 # Verify webhook signature
 signature = request.headers.get('X-Hub-Signature-256')
 secret = os.environ["GITHUB_WEBHOOK_SECRET"]

 if not verify_signature(request.data, signature, secret):
 return "Unauthorized", 401

 event = request.headers.get('X-GitHub-Event')
 payload = request.json

 # Handle different events
 if event == "pull_request":
 handle_pr(payload)
 elif event == "push":
 handle_push(payload)
 elif event == "workflow_run":
 handle_workflow(payload)

 return "OK", 200

def handle_pr(payload):
 """Notify on PR events"""
 action = payload['action']
 pr = payload['pull_request']

 if action == "opened":
 slack_client.chat_postMessage(
 channel="#platform-github",
 text=f" New PR: {pr['title']}\n{pr['html_url']}"
 )
 elif action == "closed" and pr['merged']:
 slack_client.chat_postMessage(
 channel="#platform-github",
 text=f" PR merged: {pr['title']}"
 )

def verify_signature(payload, signature, secret):
 """Verify GitHub webhook signature"""
 if not signature:
 return False

 hash_object = hmac.new(
 secret.encode('utf-8'),
 msg=payload,
 digestmod=hashlib.sha256
 )
 expected = "sha256=" + hash_object.hexdigest()

 return hmac.compare_digest(expected, signature)

if __name__ == "__main__":
 app.run(port=5000)
```

---

### Day 7: Incident Management Workflows

**What you'll learn:**
- Incident creation automation
- Status updates in threads
- Post-mortem templates
- On-call integration

**Complete incident workflow:**
```python
from slack_bolt import App
from datetime import datetime
import os

app = App(token=os.environ.get("SLACK_BOT_TOKEN"))

# Incident state (use DB in production)
incidents = {}

@app.command("/incident")
def create_incident(ack, command, client):
 """Create new incident"""
 ack()

 # Open incident creation modal
 client.views_open(
 trigger_id=command["trigger_id"],
 view={
 "type": "modal",
 "callback_id": "incident_create",
 "title": {"type": "plain_text", "text": "Create Incident"},
 "submit": {"type": "plain_text", "text": "Create"},
 "blocks": [
 {
 "type": "input",
 "block_id": "title",
 "element": {
 "type": "plain_text_input",
 "action_id": "title_input"
 },
 "label": {"type": "plain_text", "text": "Title"}
 },
 {
 "type": "input",
 "block_id": "severity",
 "element": {
 "type": "static_select",
 "action_id": "severity_select",
 "options": [
 {"text": {"type": "plain_text", "text": " P0 - Critical"}, "value": "p0"},
 {"text": {"type": "plain_text", "text": " P1 - High"}, "value": "p1"},
 {"text": {"type": "plain_text", "text": " P2 - Medium"}, "value": "p2"}
 ]
 },
 "label": {"type": "plain_text", "text": "Severity"}
 }
 ]
 }
 )

@app.view("incident_create")
def handle_incident_create(ack, body, client, view):
 """Handle incident creation"""
 ack()

 # Extract form data
 title = view["state"]["values"]["title"]["title_input"]["value"]
 severity = view["state"]["values"]["severity"]["severity_select"]["selected_option"]["value"]
 creator = body["user"]["id"]

 # Generate incident ID
 incident_id = f"INC-{len(incidents) + 1}"

 # Create incident channel
 channel_name = f"incident-{incident_id.lower()}"
 channel_response = client.conversations_create(
 name=channel_name,
 is_private=False
 )
 channel_id = channel_response["channel"]["id"]

 # Invite relevant people
 client.conversations_invite(
 channel=channel_id,
 users=creator
 )

 # Post incident details
 response = client.chat_postMessage(
 channel=channel_id,
 blocks=[
 {
 "type": "header",
 "text": {"type": "plain_text", "text": f" {incident_id}: {title}"}
 },
 {
 "type": "section",
 "fields": [
 {"type": "mrkdwn", "text": f"*Severity:*\n{severity.upper()}"},
 {"type": "mrkdwn", "text": f"*Status:*\nInvestigating"},
 {"type": "mrkdwn", "text": f"*Created by:*\n<@{creator}>"},
 {"type": "mrkdwn", "text": f"*Created:*\n{datetime.now().strftime('%Y-%m-%d %H:%M')}"
 }
 ]
 },
 {
 "type": "actions",
 "elements": [
 {
 "type": "button",
 "text": {"type": "plain_text", "text": "Update Status"},
 "action_id": "update_status",
 "value": incident_id
 },
 {
 "type": "button",
 "text": {"type": "plain_text", "text": "Resolve"},
 "action_id": "resolve_incident",
 "value": incident_id,
 "style": "primary"
 }
 ]
 }
 ]
 )

 # Store incident
 incidents[incident_id] = {
 "title": title,
 "severity": severity,
 "status": "investigating",
 "channel_id": channel_id,
 "message_ts": response["ts"],
 "created_by": creator,
 "created_at": datetime.now().isoformat()
 }

 # Announce in main channel
 client.chat_postMessage(
 channel="#platform-incidents",
 text=f" New {severity.upper()} incident: {title}\nJoin <#{channel_id}> for updates"
 )

@app.action("update_status")
def update_incident_status(ack, body, client):
 """Update incident status"""
 ack()

 incident_id = body["actions"][0]["value"]

 # Show status update modal
 client.views_open(
 trigger_id=body["trigger_id"],
 view={
 "type": "modal",
 "callback_id": f"status_update:{incident_id}",
 "title": {"type": "plain_text", "text": "Update Status"},
 "submit": {"type": "plain_text", "text": "Update"},
 "blocks": [
 {
 "type": "input",
 "block_id": "update",
 "element": {
 "type": "plain_text_input",
 "action_id": "update_input",
 "multiline": True
 },
 "label": {"type": "plain_text", "text": "Status Update"}
 }
 ]
 }
 )

@app.view_submission(lambda ack, view: "status_update:" in view["callback_id"])
def handle_status_update(ack, body, client, view):
 """Handle status update submission"""
 ack()

 callback_id = view["callback_id"]
 incident_id = callback_id.split(":")[1]

 update = view["state"]["values"]["update"]["update_input"]["value"]
 user = body["user"]["id"]

 incident = incidents[incident_id]

 # Post update to incident thread
 client.chat_postMessage(
 channel=incident["channel_id"],
 thread_ts=incident["message_ts"],
 text=f" Update from <@{user}>:\n{update}"
 )

@app.action("resolve_incident")
def resolve_incident(ack, body, client):
 """Resolve incident"""
 ack()

 incident_id = body["actions"][0]["value"]
 incident = incidents[incident_id]
 user = body["user"]["id"]

 # Update incident status
 incident["status"] = "resolved"
 incident["resolved_at"] = datetime.now().isoformat()
 incident["resolved_by"] = user

 # Update original message
 client.chat_update(
 channel=incident["channel_id"],
 ts=incident["message_ts"],
 blocks=[
 {
 "type": "header",
 "text": {"type": "plain_text", "text": f" {incident_id}: {incident['title']} (RESOLVED)"}
 },
 {
 "type": "section",
 "text": {"type": "mrkdwn", "text": f"Resolved by <@{user}>"}
 }
 ]
 )

 # Announce resolution
 client.chat_postMessage(
 channel="#platform-incidents",
 text=f" {incident_id} resolved by <@{user}>"
 )

 # Archive channel (optional)
 # client.conversations_archive(channel=incident["channel_id"])
```

---

## Exercises

### Exercise 1: Build Alert Aggregator

Implement a 5-minute batching aggregator for test failures.

### Exercise 2: Smart Channel Router

Create a router that sends:
- P0/P1 → #critical
- P2 → #alerts
- All pipeline failures → #pipelines

### Exercise 3: Complete Tekton Integration

Build end-to-end Tekton → Slack integration with:
- Start notification (creates thread)
- Progress updates (in thread)
- Completion (updates parent message)
- Buttons for rerun/logs

### Exercise 4: Incident Management

Implement full incident workflow:
- `/incident` creates channel + initial message
- Status updates in thread
- Resolution with post-mortem template

---

## Week 3 Checklist

- [ ] Implemented alert batching
- [ ] Built deduplication logic
- [ ] Created smart channel routing
- [ ] Enriched alerts with context
- [ ] Integrated with Tekton pipelines
- [ ] Built incident management workflow
- [ ] Reduced noise by 80%+

---

## Resources

- **Alert Fatigue:** https://increment.com/on-call/alert-fatigue/
- **SRE Book - Monitoring:** https://sre.google/sre-book/monitoring-distributed-systems/
- **Slack Best Practices:** https://api.slack.com/best-practices

---

## Production-Ready Checklist

Before deploying to production:

**Architecture:**
- [ ] Use persistent storage (Redis/DB) for state
- [ ] Deploy bot in HA mode (multiple instances)
- [ ] Implement health checks
- [ ] Add metrics & logging

**Security:**
- [ ] Rotate tokens regularly
- [ ] Use secret management (Vault, k8s secrets)
- [ ] Validate all webhook signatures
- [ ] Implement rate limiting

**Reliability:**
- [ ] Handle Slack API rate limits
- [ ] Retry failed API calls
- [ ] Circuit breaker for external dependencies
- [ ] Graceful degradation

**Observability:**
- [ ] Log all sent messages
- [ ] Track alert volumes
- [ ] Monitor bot uptime
- [ ] Dashboard for key metrics

---

## Final Project: Complete Platform Slack Integration

Build production-ready Slack integration for Platform with:

1. **Pipeline Notifications:**
 - Success/failure alerts with threading
 - Aggregated hourly summary
 - Interactive rerun buttons

2. **Incident Management:**
 - Auto-create channels for P0/P1 alerts
 - Status update workflow
 - Post-mortem templates

3. **Smart Routing:**
 - Severity-based channel routing
 - Deduplication (1 hour window)
 - Context enrichment (recent failures, on-call)

4. **Slash Commands:**
 - `/pipeline status` - Current status
 - `/pipeline rerun <name>` - Rerun pipeline
 - `/incident` - Create incident

---

**Congratulations!** You now have the skills to build production-grade Slack automation! 

**Keep learning:**
- Explore Slack Workflow Builder (no-code)
- Try Slack Functions (serverless)
- Build Slack app for distribution
