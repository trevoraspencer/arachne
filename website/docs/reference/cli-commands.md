---
sidebar_position: 1
title: "CLI Commands Reference"
description: "Comprehensive reference for all arachne CLI commands and slash commands"
---

# CLI Commands Reference

## Terminal Commands

These are commands you run from your shell.

### Core Commands

| Command | Description |
|---------|-------------|
| `arachne` | Start interactive chat (default) |
| `arachne chat -q "Hello"` | Single query mode (non-interactive) |
| `arachne chat --continue` / `-c` | Resume the most recent session |
| `arachne chat --resume <id>` / `-r <id>` | Resume a specific session |
| `arachne chat --model <name>` | Use a specific model |
| `arachne chat --provider <name>` | Force a provider (`nous`, `openrouter`, `zai`, `kimi-coding`, `minimax`, `minimax-cn`) |
| `arachne chat --toolsets "web,terminal"` / `-t` | Use specific toolsets |
| `arachne chat --verbose` | Enable verbose/debug output |

### Provider & Model Management

| Command | Description |
|---------|-------------|
| `arachne model` | Switch provider and model interactively |
| `arachne login` | OAuth login to a provider (use `--provider` to specify) |
| `arachne logout` | Clear provider authentication |

### Configuration

| Command | Description |
|---------|-------------|
| `arachne setup` | Full setup wizard (provider, terminal, messaging) |
| `arachne config` | View current configuration |
| `arachne config edit` | Open config.yaml in your editor |
| `arachne config set KEY VAL` | Set a specific value |
| `arachne config check` | Check for missing config (useful after updates) |
| `arachne config migrate` | Interactively add missing options |
| `arachne tools` | Interactive tool configuration per platform |
| `arachne status` | Show configuration status (including auth) |
| `arachne doctor` | Diagnose issues |

### Maintenance

| Command | Description |
|---------|-------------|
| `arachne update` | Update to latest version |
| `arachne uninstall` | Uninstall (can keep configs for later reinstall) |
| `arachne version` | Show version info |

### Gateway (Messaging + Cron)

| Command | Description |
|---------|-------------|
| `arachne gateway` | Run gateway in foreground |
| `arachne gateway setup` | Configure messaging platforms interactively |
| `arachne gateway install` | Install as system service (Linux/macOS) |
| `arachne gateway start` | Start the service |
| `arachne gateway stop` | Stop the service |
| `arachne gateway restart` | Restart the service |
| `arachne gateway status` | Check service status |
| `arachne gateway uninstall` | Uninstall the system service |
| `arachne whatsapp` | Pair WhatsApp via QR code |

### Skills

| Command | Description |
|---------|-------------|
| `arachne skills browse` | Browse all available skills with pagination (official first) |
| `arachne skills search <query>` | Search skill registries |
| `arachne skills install <identifier>` | Install a skill (with security scan) |
| `arachne skills inspect <identifier>` | Preview before installing |
| `arachne skills list` | List installed skills |
| `arachne skills list --source hub` | List hub-installed skills only |
| `arachne skills audit` | Re-scan all hub skills |
| `arachne skills uninstall <name>` | Remove a hub skill |
| `arachne skills publish <path> --to github --repo owner/repo` | Publish a skill |
| `arachne skills snapshot export <file>` | Export skill config |
| `arachne skills snapshot import <file>` | Import from snapshot |
| `arachne skills tap add <repo>` | Add a custom source |
| `arachne skills tap remove <repo>` | Remove a source |
| `arachne skills tap list` | List custom sources |

### Cron & Pairing

| Command | Description |
|---------|-------------|
| `arachne cron list` | View scheduled jobs |
| `arachne cron status` | Check if cron scheduler is running |
| `arachne cron tick` | Manually trigger a cron tick |
| `arachne pairing list` | View pending + approved users |
| `arachne pairing approve <platform> <code>` | Approve a pairing code |
| `arachne pairing revoke <platform> <user_id>` | Remove user access |
| `arachne pairing clear-pending` | Clear all pending pairing requests |

### Sessions

| Command | Description |
|---------|-------------|
| `arachne sessions list` | Browse past sessions |
| `arachne sessions export <id>` | Export a session |
| `arachne sessions delete <id>` | Delete a specific session |
| `arachne sessions prune` | Remove old sessions |
| `arachne sessions stats` | Show session statistics |

### Insights

| Command | Description |
|---------|-------------|
| `arachne insights` | Show usage analytics for the last 30 days |
| `arachne insights --days 7` | Analyze a custom time window |
| `arachne insights --source telegram` | Filter by platform |

---

## Slash Commands (Inside Chat)

Type `/` in the interactive CLI to see an autocomplete dropdown.

### Navigation & Control

| Command | Description |
|---------|-------------|
| `/help` | Show available commands |
| `/quit` | Exit the CLI (aliases: `/exit`, `/q`) |
| `/clear` | Clear screen and reset conversation |
| `/new` | Start a new conversation |
| `/reset` | Reset conversation only (keep screen) |

### Tools & Configuration

| Command | Description |
|---------|-------------|
| `/tools` | List all available tools |
| `/toolsets` | List available toolsets |
| `/model [name]` | Show or change the current model |
| `/config` | Show current configuration |
| `/prompt [text]` | View/set custom system prompt |
| `/personality [name]` | Set a predefined personality |

### Conversation

| Command | Description |
|---------|-------------|
| `/history` | Show conversation history |
| `/retry` | Retry the last message |
| `/undo` | Remove the last user/assistant exchange |
| `/save` | Save the current conversation |
| `/compress` | Manually compress conversation context |
| `/usage` | Show token usage for this session |
| `/insights [--days N]` | Show usage insights and analytics (last 30 days) |

### Media & Input

| Command | Description |
|---------|-------------|
| `/paste` | Check clipboard for an image and attach it (see [Vision & Image Paste](/docs/user-guide/features/vision)) |

### Skills & Scheduling

| Command | Description |
|---------|-------------|
| `/cron` | Manage scheduled tasks |
| `/skills` | Browse, search, install, inspect, or manage skills |
| `/platforms` | Show gateway/messaging platform status |
| `/verbose` | Cycle tool progress: off → new → all → verbose |
| `/<skill-name>` | Invoke any installed skill |

### Gateway-Only Commands

These work in messaging platforms (Telegram, Discord, Slack, WhatsApp) but not the interactive CLI:

| Command | Description |
|---------|-------------|
| `/stop` | Stop the running agent (no follow-up message) |
| `/sethome` | Set this chat as the home channel |
| `/status` | Show session info |
| `/reload-mcp` | Reload MCP servers from config |
| `/update` | Update Arachne Agent to the latest version |

---

## Keybindings

| Key | Action |
|-----|--------|
| `Enter` | Send message |
| `Alt+Enter` / `Ctrl+J` | New line (multi-line input) |
| `Alt+V` | Paste image from clipboard (see [Vision & Image Paste](/docs/user-guide/features/vision)) |
| `Ctrl+V` | Paste text + auto-check for clipboard image |
| `Ctrl+C` | Clear input/images, interrupt agent, or exit (contextual) |
| `Ctrl+D` | Exit |
| `Tab` | Autocomplete slash commands |

:::tip
Commands are case-insensitive — `/HELP` works the same as `/help`.
:::

:::info Image paste keybindings
`Alt+V` works in most terminals but **not** in VSCode's integrated terminal (VSCode intercepts Alt+key combos). `Ctrl+V` only triggers an image check when the clipboard also contains text (terminals don't send paste events for image-only clipboard). The `/paste` command is the universal fallback. See the [full compatibility table](/docs/user-guide/features/vision#platform-compatibility).
:::
