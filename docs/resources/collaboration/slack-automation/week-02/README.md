# Slack Week 2: Slack API + Bot Development

## Goal

Build custom Slack bots with interactive components using the Bolt framework.

---

## Topics

### Day 1-2: Slack API Basics + Bolt Framework Setup

**What you'll learn:**
- Slack Web API vs Webhooks
- OAuth & Bot tokens
- Slack Bolt framework (Python)
- Bot permissions & scopes

**Setup Bolt app:**
```bash
# Install Slack SDK
pip install slack-bolt slack-sdk

# Or with pip
python3 -m pip install slack-bolt
```

**Create a Bot User:**
```
1. Go to: https://api.slack.com/apps
2. Select your app (or create new)
3. OAuth & Permissions → Scopes
4. Add Bot Token Scopes:
 - chat:write (send messages)
 - chat:write.public (post to any channel)
 - channels:read (list channels)
 - users:read (get user info)
 - commands (slash commands)

5. Install App to Workspace
6. Copy "Bot User OAuth Token" (xoxb-...)
```

**First Bolt app:**
```python
#!/usr/bin/env python3
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler
import os

# Initialize app with bot token
app = App(token=os.environ.get("SLACK_BOT_TOKEN"))

# Respond to "hello" message
@app.message("hello")
def say_hello(message, say):
 user = message['user']
 say(f"Hi <@{user}>! ")

# Start app in Socket Mode (for development)
if __name__ == "__main__":
 # Requires Socket Mode token (xapp-...)
 handler = SocketModeHandler(app, os.environ["SLACK_APP_TOKEN"])
 handler.start()
```

**Enable Socket Mode (for development):**
```
App Settings → Socket Mode → Enable
Generate App-Level Token (xapp-...)
Scope: connections:write
```

**Run the bot:**
```bash
export SLACK_BOT_TOKEN="xoxb-your-bot-token"
export SLACK_APP_TOKEN="xapp-your-app-token"
python3 bot.py

# In Slack, type "hello" in any channel
# Bot responds: "Hi @mgreczi! "
```

**Key concepts:**
- **Bot Token (xoxb-):** Acts on behalf of bot user
- **App Token (xapp-):** For Socket Mode connections
- **Socket Mode:** Development mode, no public URL needed
- **HTTP Mode:** Production mode, requires webhook URL

---

### Day 3-4: Interactive Components (Buttons, Modals)

**What you'll learn:**
- Action buttons in messages
- Button click handlers
- Modal dialogs
- Form inputs

**Message with buttons:**
```python
from slack_bolt import App
import os

app = App(token=os.environ.get("SLACK_BOT_TOKEN"))

# Command to trigger pipeline action
@app.command("/pipeline")
def handle_pipeline_command(ack, command, client):
 # Acknowledge command
 ack()

 # Send message with buttons
 client.chat_postMessage(
 channel=command["channel_id"],
 text="Pipeline Management",
 blocks=[
 {
 "type": "section",
 "text": {
 "type": "mrkdwn",
 "text": "What would you like to do?"
 }
 },
 {
 "type": "actions",
 "elements": [
 {
 "type": "button",
 "text": {"type": "plain_text", "text": "Rerun Failed"},
 "value": "rerun_failed",
 "action_id": "rerun_failed_button"
 },
 {
 "type": "button",
 "text": {"type": "plain_text", "text": "View Logs"},
 "value": "view_logs",
 "action_id": "view_logs_button",
 "style": "primary"
 },
 {
 "type": "button",
 "text": {"type": "plain_text", "text": "Cancel"},
 "value": "cancel",
 "action_id": "cancel_button",
 "style": "danger"
 }
 ]
 }
 ]
 )

# Handle button clicks
@app.action("rerun_failed_button")
def handle_rerun(ack, body, client):
 ack()
 # Trigger pipeline rerun (call Tekton API)
 client.chat_postMessage(
 channel=body["channel"]["id"],
 text=" Rerunning failed pipelines..."
 )

@app.action("view_logs_button")
def handle_view_logs(ack, body, client):
 ack()
 logs_url = "https://console.redhat.com/logs"
 client.chat_postMessage(
 channel=body["channel"]["id"],
 text=f" View logs: {logs_url}"
 )

@app.action("cancel_button")
def handle_cancel(ack, body, client):
 ack()
 client.chat_postMessage(
 channel=body["channel"]["id"],
 text=" Cancelled."
 )
```

**Modal dialog (form input):**
```python
@app.command("/create-incident")
def open_incident_modal(ack, body, client):
 ack()

 # Open modal
 client.views_open(
 trigger_id=body["trigger_id"],
 view={
 "type": "modal",
 "callback_id": "incident_modal",
 "title": {"type": "plain_text", "text": "Create Incident"},
 "submit": {"type": "plain_text", "text": "Create"},
 "close": {"type": "plain_text", "text": "Cancel"},
 "blocks": [
 {
 "type": "input",
 "block_id": "title_block",
 "element": {
 "type": "plain_text_input",
 "action_id": "title_input",
 "placeholder": {
 "type": "plain_text",
 "text": "Incident title"
 }
 },
 "label": {"type": "plain_text", "text": "Title"}
 },
 {
 "type": "input",
 "block_id": "severity_block",
 "element": {
 "type": "static_select",
 "action_id": "severity_select",
 "placeholder": {
 "type": "plain_text",
 "text": "Select severity"
 },
 "options": [
 {
 "text": {"type": "plain_text", "text": " Critical"},
 "value": "critical"
 },
 {
 "text": {"type": "plain_text", "text": " High"},
 "value": "high"
 },
 {
 "text": {"type": "plain_text", "text": " Medium"},
 "value": "medium"
 }
 ]
 },
 "label": {"type": "plain_text", "text": "Severity"}
 }
 ]
 }
 )

# Handle modal submission
@app.view("incident_modal")
def handle_incident_submission(ack, body, client, view):
 ack()

 # Extract form values
 title = view["state"]["values"]["title_block"]["title_input"]["value"]
 severity = view["state"]["values"]["severity_block"]["severity_select"]["selected_option"]["value"]

 # Create incident (call your incident management API)
 client.chat_postMessage(
 channel="#konflux-incidents",
 text=f" New incident created: *{title}* (Severity: {severity})"
 )
```

---

### Day 5-6: Event Subscriptions & Message Threading

**What you'll learn:**
- Listen to Slack events
- React to messages/reactions
- Thread messages for context
- Maintaining conversation state

**Enable Event Subscriptions:**
```
App Settings → Event Subscriptions → Enable
(For Socket Mode, no webhook URL needed)

Subscribe to bot events:
- message.channels (messages in channels)
- app_mention (when bot is @mentioned)
- reaction_added (when emoji reaction added)
```

**Listen to events:**
```python
# React when bot is mentioned
@app.event("app_mention")
def handle_mention(event, say, client):
 user = event['user']
 text = event['text']

 # Respond in thread
 say(
 text=f"Hi <@{user}>! You said: {text}",
 thread_ts=event['ts'] # Reply in thread
 )

# React to emoji reactions
@app.event("reaction_added")
def handle_reaction(event, client):
 # If someone reacts with to a pipeline failure, acknowledge it
 if event['reaction'] == 'white_check_mark':
 channel = event['item']['channel']
 ts = event['item']['ts']

 # Update original message
 client.chat_update(
 channel=channel,
 ts=ts,
 text=" Acknowledged by team"
 )
```

**Message threading example (pipeline updates):**
```python
def send_pipeline_start(channel, pipeline_name):
 """Send initial pipeline message, return thread_ts"""
 response = client.chat_postMessage(
 channel=channel,
 text=f"⏳ Pipeline started: {pipeline_name}"
 )
 return response['ts'] # Save this as thread_ts

def send_pipeline_update(channel, thread_ts, update):
 """Send update in the same thread"""
 client.chat_postMessage(
 channel=channel,
 thread_ts=thread_ts, # Reply to original message
 text=update
 )

def send_pipeline_complete(channel, thread_ts, status):
 """Final update in thread + update parent message"""
 emoji = "" if status == "success" else ""

 # Update original message
 client.chat_update(
 channel=channel,
 ts=thread_ts,
 text=f"{emoji} Pipeline {status}"
 )

 # Add final comment in thread
 client.chat_postMessage(
 channel=channel,
 thread_ts=thread_ts,
 text=f"Pipeline completed with status: {status}"
 )

# Usage
thread_ts = send_pipeline_start("#konflux-builds", "build-service-123")
send_pipeline_update("#konflux-builds", thread_ts, " Building image...")
send_pipeline_update("#konflux-builds", thread_ts, " Running tests...")
send_pipeline_complete("#konflux-builds", thread_ts, "success")
```

---

### Day 7: Slash Commands & Shortcuts

**What you'll learn:**
- Custom slash commands
- Global shortcuts
- Message shortcuts
- Command parsing

**Create slash command:**
```
App Settings → Slash Commands → Create New Command

Command: /pipeline
Request URL: (not needed for Socket Mode)
Description: Manage Platform pipelines
```

**Handle slash command:**
```python
@app.command("/pipeline")
def handle_pipeline_cmd(ack, command, respond):
 ack()

 # Parse command text
 # /pipeline list
 # /pipeline rerun build-123
 # /pipeline status

 cmd_text = command['text'].strip()

 if cmd_text == "list":
 # List recent pipelines
 respond(" Recent pipelines:\n• build-service-123 \n• image-build-456 ")

 elif cmd_text.startswith("rerun"):
 # Extract pipeline name
 pipeline = cmd_text.split()[1] if len(cmd_text.split()) > 1 else None
 if pipeline:
 # Trigger rerun (call Tekton API)
 respond(f" Rerunning pipeline: {pipeline}")
 else:
 respond(" Usage: /pipeline rerun <pipeline-name>")

 elif cmd_text == "status":
 respond(" All systems operational")

 else:
 respond("Available commands:\n• /pipeline list\n• /pipeline rerun <name>\n• /pipeline status")
```

**Global shortcut:**
```python
@app.shortcut("create_incident")
def handle_shortcut(ack, shortcut, client):
 ack()
 # Open incident creation modal
 # (same as modal example from Day 3-4)
```

---

## Exercises

### Exercise 1: Build a Pipeline Status Bot

Create a bot that responds to `/pipeline status`:

```python
#!/usr/bin/env python3
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler
import os

app = App(token=os.environ.get("SLACK_BOT_TOKEN"))

@app.command("/pipeline")
def handle_pipeline(ack, command, respond):
 ack()

 # Mock pipeline data (replace with real Tekton API call)
 pipelines = [
 {"name": "build-service", "status": "success", "duration": "3m 21s"},
 {"name": "test-suite", "status": "success", "duration": "5m 12s"},
 {"name": "image-build", "status": "failed", "duration": "2m 45s"},
 ]

 # Build response
 blocks = [
 {
 "type": "header",
 "text": {"type": "plain_text", "text": " Pipeline Status"}
 }
 ]

 for p in pipelines:
 emoji = "" if p["status"] == "success" else ""
 blocks.append({
 "type": "section",
 "text": {
 "type": "mrkdwn",
 "text": f"{emoji} *{p['name']}* - {p['duration']}"
 }
 })

 respond(blocks=blocks)

if __name__ == "__main__":
 handler = SocketModeHandler(app, os.environ["SLACK_APP_TOKEN"])
 handler.start()
```

---

### Exercise 2: Interactive Pipeline Actions

Add buttons to rerun failed pipelines:

```python
@app.command("/pipeline")
def handle_pipeline(ack, command, client):
 ack()

 # Send message with action buttons
 client.chat_postMessage(
 channel=command["channel_id"],
 blocks=[
 {
 "type": "section",
 "text": {"type": "mrkdwn", "text": " *image-build* failed"}
 },
 {
 "type": "actions",
 "elements": [
 {
 "type": "button",
 "text": {"type": "plain_text", "text": " Rerun"},
 "action_id": "rerun_pipeline",
 "value": "image-build"
 },
 {
 "type": "button",
 "text": {"type": "plain_text", "text": " View Logs"},
 "action_id": "view_logs",
 "url": "https://console.redhat.com/logs/image-build"
 }
 ]
 }
 ]
 )

@app.action("rerun_pipeline")
def handle_rerun(ack, body, say):
 ack()
 pipeline = body["actions"][0]["value"]
 say(f" Rerunning pipeline: {pipeline}")
```

---

### Exercise 3: Threaded Pipeline Updates

Implement pipeline lifecycle tracking with threads:

```python
# Store thread_ts in database or cache (simplified here)
pipeline_threads = {}

@app.command("/start-pipeline")
def start_pipeline(ack, command, client):
 ack()

 pipeline_name = command['text'] or "test-pipeline"

 # Send initial message
 response = client.chat_postMessage(
 channel=command["channel_id"],
 text=f"⏳ Starting pipeline: {pipeline_name}"
 )

 # Save thread_ts
 thread_ts = response['ts']
 pipeline_threads[pipeline_name] = {
 "thread_ts": thread_ts,
 "channel": command["channel_id"]
 }

 # Simulate pipeline steps (in real app, listen to Tekton events)
 import time
 import threading

 def run_pipeline():
 time.sleep(2)
 client.chat_postMessage(
 channel=command["channel_id"],
 thread_ts=thread_ts,
 text=" Building image..."
 )

 time.sleep(3)
 client.chat_postMessage(
 channel=command["channel_id"],
 thread_ts=thread_ts,
 text=" Running tests..."
 )

 time.sleep(2)
 # Update parent message
 client.chat_update(
 channel=command["channel_id"],
 ts=thread_ts,
 text=f" Pipeline succeeded: {pipeline_name}"
 )

 threading.Thread(target=run_pipeline).start()
```

---

## Week 2 Checklist

- [ ] Set up Slack Bolt app with Socket Mode
- [ ] Created bot that responds to messages
- [ ] Built interactive buttons
- [ ] Created modal form dialog
- [ ] Implemented event listeners
- [ ] Used message threading
- [ ] Created slash command
- [ ] Understand OAuth & bot scopes

---

## Resources

- **Bolt for Python:** https://slack.dev/bolt-python/
- **Slack API Reference:** https://api.slack.com/methods
- **Block Kit Builder:** https://app.slack.com/block-kit-builder/
- **Socket Mode:** https://api.slack.com/apis/connections/socket

---

## Real-World Integration

Complete example: Tekton Pipeline → Slack Bot

```python
#!/usr/bin/env python3
"""
Platform Pipeline Bot
Listens to Tekton events and sends Slack notifications with threading
"""
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler
import os
import requests

app = App(token=os.environ.get("SLACK_BOT_TOKEN"))

# In-memory storage (use Redis/DB in production)
pipeline_threads = {}

def send_pipeline_notification(pipeline_run, status, channel="#konflux-builds"):
 """Send or update pipeline notification"""

 pipeline_name = pipeline_run['metadata']['name']
 namespace = pipeline_run['metadata']['namespace']

 # Check if we already have a thread for this pipeline
 if pipeline_name in pipeline_threads:
 # Update existing message
 thread_ts = pipeline_threads[pipeline_name]['thread_ts']

 emoji = "" if status == "Succeeded" else "" if status == "Failed" else "⏳"

 app.client.chat_update(
 channel=channel,
 ts=thread_ts,
 text=f"{emoji} Pipeline {status}: {pipeline_name}"
 )

 # Add update to thread
 app.client.chat_postMessage(
 channel=channel,
 thread_ts=thread_ts,
 text=f"Status update: {status}"
 )

 else:
 # Create new message thread
 response = app.client.chat_postMessage(
 channel=channel,
 text=f"⏳ Pipeline started: {pipeline_name}",
 blocks=[
 {
 "type": "section",
 "text": {
 "type": "mrkdwn",
 "text": f"*Pipeline:* {pipeline_name}\n*Namespace:* {namespace}"
 }
 }
 ]
 )

 # Save thread
 pipeline_threads[pipeline_name] = {
 "thread_ts": response['ts'],
 "channel": channel
 }

# Slash command to check pipeline status
@app.command("/pipeline")
def check_pipeline(ack, command, respond):
 ack()
 # Query Tekton API for pipeline status
 respond(" Fetching pipeline status...")

if __name__ == "__main__":
 handler = SocketModeHandler(app, os.environ["SLACK_APP_TOKEN"])
 handler.start()
```

---

**Next:** [Week 3: Workflow Optimization & Advanced Patterns →](../week-03/README.md)
