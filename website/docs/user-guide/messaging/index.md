---
sidebar_position: 1
title: "Messaging Gateway"
description: "Chat with Arachne from Telegram, Discord, Slack, or WhatsApp — architecture and setup overview"
---

# Messaging Gateway

Chat with Arachne from Telegram, Discord, Slack, or WhatsApp. The gateway is a single background process that connects to all your configured platforms, handles sessions, runs cron jobs, and delivers voice messages.

## Architecture

```text
┌─────────────────────────────────────────────────────────────────┐
│                      Arachne Gateway                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │
│  │ Telegram │ │ Discord  │ │ WhatsApp │ │  Slack   │           │
│  │ Adapter  │ │ Adapter  │ │ Adapter  │ │ Adapter  │           │
│  └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘           │
│       │             │            │             │                │
│       └─────────────┼────────────┼─────────────┘                │
│                           │                                     │
│                  ┌────────▼────────┐                            │
│                  │  Session Store  │                            │
│                  │  (per-chat)     │                            │
│                  └────────┬────────┘                            │
│                           │                                     │
│                  ┌────────▼────────┐                            │
│                  │   AIAgent       │                            │
│                  │   (run_agent)   │                            │
│                  └─────────────────┘                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

Each platform adapter receives messages, routes them through a per-chat session store, and dispatches them to the AIAgent for processing. The gateway also runs the cron scheduler, ticking every 60 seconds to execute any due jobs.

## Quick Setup

The easiest way to configure messaging platforms is the interactive wizard:

```bash
arachne gateway setup        # Interactive setup for all messaging platforms
```

This walks you through configuring each platform with arrow-key selection, shows which platforms are already configured, and offers to start/restart the gateway when done.

## Gateway Commands

```bash
arachne gateway              # Run in foreground
arachne gateway setup        # Configure messaging platforms interactively
arachne gateway install      # Install as systemd service (Linux) / launchd (macOS)
arachne gateway start        # Start the service
arachne gateway stop         # Stop the service
arachne gateway status       # Check service status
```

## Chat Commands (Inside Messaging)

| Command | Description |
|---------|-------------|
| `/new` or `/reset` | Start fresh conversation |
| `/model [name]` | Show or change the model |
| `/personality [name]` | Set a personality |
| `/retry` | Retry the last message |
| `/undo` | Remove the last exchange |
| `/status` | Show session info |
| `/stop` | Stop the running agent |
| `/sethome` | Set this chat as the home channel |
| `/compress` | Manually compress conversation context |
| `/usage` | Show token usage for this session |
| `/insights [days]` | Show usage insights and analytics |
| `/reload-mcp` | Reload MCP servers from config |
| `/update` | Update Arachne Agent to the latest version |
| `/help` | Show available commands |
| `/<skill-name>` | Invoke any installed skill |

## Session Management

### Session Persistence

Sessions persist across messages until they reset. The agent remembers your conversation context.

### Reset Policies

Sessions reset based on configurable policies:

| Policy | Default | Description |
|--------|---------|-------------|
| Daily | 4:00 AM | Reset at a specific hour each day |
| Idle | 120 min | Reset after N minutes of inactivity |
| Both | (combined) | Whichever triggers first |

Configure per-platform overrides in `~/.arachne/gateway.json`:

```json
{
  "reset_by_platform": {
    "telegram": { "mode": "idle", "idle_minutes": 240 },
    "discord": { "mode": "idle", "idle_minutes": 60 }
  }
}
```

## Security

**By default, the gateway denies all users who are not in an allowlist or paired via DM.** This is the safe default for a bot with terminal access.

```bash
# Restrict to specific users (recommended):
TELEGRAM_ALLOWED_USERS=123456789,987654321
DISCORD_ALLOWED_USERS=123456789012345678

# Or allow specific users across all platforms (comma-separated user IDs):
GATEWAY_ALLOWED_USERS=123456789,987654321

# Or explicitly allow all users (NOT recommended for bots with terminal access):
GATEWAY_ALLOW_ALL_USERS=true
```

### DM Pairing (Alternative to Allowlists)

Instead of manually configuring user IDs, unknown users receive a one-time pairing code when they DM the bot:

```bash
# The user sees: "Pairing code: XKGH5N7P"
# You approve them with:
arachne pairing approve telegram XKGH5N7P

# Other pairing commands:
arachne pairing list          # View pending + approved users
arachne pairing revoke telegram 123456789  # Remove access
```

Pairing codes expire after 1 hour, are rate-limited, and use cryptographic randomness.

## Interrupting the Agent

Send any message while the agent is working to interrupt it. Key behaviors:

- **In-progress terminal commands are killed immediately** (SIGTERM, then SIGKILL after 1s)
- **Tool calls are cancelled** — only the currently-executing one runs, the rest are skipped
- **Multiple messages are combined** — messages sent during interruption are joined into one prompt
- **`/stop` command** — interrupts without queuing a follow-up message

## Tool Progress Notifications

Control how much tool activity is displayed in `~/.arachne/config.yaml`:

```yaml
display:
  tool_progress: all    # off | new | all | verbose
```

When enabled, the bot sends status messages as it works:

```text
💻 `ls -la`...
🔍 web_search...
📄 web_extract...
🐍 execute_code...
```

## Service Management

### Linux (systemd)

```bash
arachne gateway install               # Install as user service
systemctl --user start arachne-gateway
systemctl --user stop arachne-gateway
systemctl --user status arachne-gateway
journalctl --user -u arachne-gateway -f

# Enable lingering (keeps running after logout)
sudo loginctl enable-linger $USER
```

### macOS (launchd)

```bash
arachne gateway install
launchctl start ai.arachne.gateway
launchctl stop ai.arachne.gateway
tail -f ~/.arachne/logs/gateway.log
```

## Platform-Specific Toolsets

Each platform has its own toolset:

| Platform | Toolset | Capabilities |
|----------|---------|--------------|
| CLI | `arachne-cli` | Full access |
| Telegram | `arachne-telegram` | Full tools including terminal |
| Discord | `arachne-discord` | Full tools including terminal |
| WhatsApp | `arachne-whatsapp` | Full tools including terminal |
| Slack | `arachne-slack` | Full tools including terminal |

## Next Steps

- [Telegram Setup](telegram.md)
- [Discord Setup](discord.md)
- [Slack Setup](slack.md)
- [WhatsApp Setup](whatsapp.md)
