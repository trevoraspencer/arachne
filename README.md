# Arachne

> A fork of [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent), extended with additional capabilities.

---

Arachne is a persistent personal agent — she lives on your server, remembers what she learns, and never stops running. Install her on a machine, give her your messaging accounts, and she becomes an autonomous presence that grows the longer she runs.

**Arachne builds the web.**

Arachne is fully useful on its own. It also happens to serve as the persistent agent layer within [Spiderweb](https://github.com/trevoraspencer/spiderweb), a personal AI operating system — but Spiderweb is not required, and Arachne makes no assumptions about it.

---

[![License: MIT](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)
[![Forked from hermes-agent](https://img.shields.io/badge/fork-NousResearch%2Fhermes--agent-blueviolet?style=for-the-badge)](https://github.com/NousResearch/hermes-agent)

---

## Fork

Arachne is a fork of [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent). The full hermes-agent feature set is preserved and extended. Upstream changes are pulled in selectively. Model name strings (e.g. `NousResearch/Hermes-3-*`) are preserved as-is throughout the codebase — only agent framework references are renamed.

| Area | Detail |
|---|---|
| **Naming** | All framework references renamed from `hermes` to `arachne` throughout — CLI, modules, constants, env vars, config paths |
| **Identity** | Standalone agent; also usable within a broader personal AI operating system such as [Spiderweb](https://github.com/trevoraspencer/spiderweb) |
| **Additions** | Additional capabilities layered on top of the upstream feature set |

---

## Spiderweb

Arachne was built to stand alone. It also serves as a component within [Spiderweb](https://github.com/trevoraspencer/spiderweb), a personal AI operating system. Spiderweb is a private project — no dependency on it is required to use Arachne.

For context, two sibling projects are developed alongside Arachne under the same umbrella:

| Project | Role |
|---|---|
| **Arachne** | Persistent conversational agent — this project |
| **Spindle** | CLI/TUI coding agent, similar to Claude Code or Codex |
| **Weaver** | Event-driven bot that wakes on hooks or cron events and uses Spindle programmatically to execute work |

Each is independently useful. Spiderweb is the system that puts them to work together — but that is a separate concern from this project.

---

## Quick Install

**Linux/macOS:**
```bash
curl -fsSL https://raw.githubusercontent.com/trevoraspencer/arachne/main/scripts/install.sh | bash
```

**Windows (PowerShell):**
```powershell
irm https://raw.githubusercontent.com/trevoraspencer/arachne/main/scripts/install.ps1 | iex
```

The installer will:
- Install [uv](https://docs.astral.sh/uv/) if not present
- Install Python 3.11 via uv if not already available (no sudo needed)
- Clone to `~/.arachne/arachne` (with submodules: mini-swe-agent, tinker-atropos)
- Create a virtual environment with Python 3.11
- Install all dependencies and submodule packages
- Symlink `arachne` into `~/.local/bin` so it works globally
- Run the interactive setup wizard

After installation, reload your shell and run:
```bash
source ~/.bashrc   # or: source ~/.zshrc
arachne setup      # Configure API keys (if you skipped during install)
arachne            # Start chatting
```

---

## Getting Started

```bash
arachne            # Interactive chat
arachne model      # Switch provider or model interactively
arachne tools      # See all available tools
```

### Recommended: Run with a Sandboxed Terminal

By default, Arachne runs commands on your local machine. For safer use, run with a sandboxed terminal backend so the agent cannot access its own code, config, or API keys:

```bash
# Option A: SSH into a separate machine (recommended for production)
arachne config set terminal.backend ssh
arachne config set TERMINAL_SSH_HOST my-server.example.com
arachne config set TERMINAL_SSH_USER myuser

# Option B: Docker container (local isolation)
arachne config set terminal.backend docker

# Option C: Modal cloud sandbox (serverless)
arachne config set terminal.backend modal
```

---

## Updating

```bash
arachne update
```

**Uninstalling:**
```bash
arachne uninstall
# Or manually:
rm -f ~/.local/bin/arachne
rm -rf /path/to/arachne
rm -rf ~/.arachne   # Optional — keep if you plan to reinstall
```

---

## Inference Providers

You need at least one way to connect to an LLM. Use `arachne model` to switch providers and models interactively, or configure directly:

| Provider | Setup |
|---|---|
| **Nous Portal** | `arachne login` (OAuth, subscription-based) |
| **OpenRouter** | `OPENROUTER_API_KEY` in `~/.arachne/.env` |
| **Custom Endpoint** | `OPENAI_BASE_URL` + `OPENAI_API_KEY` in `~/.arachne/.env` |

**Note:** Even when using Nous Portal or a custom endpoint, some tools (vision, web summarization, MoA) use OpenRouter independently. An `OPENROUTER_API_KEY` enables these tools.

---

---

## Configuration

All settings live in `~/.arachne/`:

```
~/.arachne/
├── config.yaml     # Settings (model, terminal, TTS, compression, etc.)
├── .env            # API keys and secrets
├── auth.json       # OAuth credentials
├── SOUL.md         # Optional: global persona
├── memories/       # Persistent memory (MEMORY.md, USER.md)
├── skills/         # Agent-created skills
├── cron/           # Scheduled jobs
├── sessions/       # Gateway sessions
└── logs/           # Logs
```

```bash
arachne config              # View current configuration
arachne config edit         # Open config.yaml in your editor
arachne config set KEY VAL  # Set a specific value
arachne config check        # Check for missing options
arachne config migrate      # Interactively add missing options

# Examples:
arachne config set model anthropic/claude-opus-4
arachne config set terminal.backend docker
arachne config set OPENROUTER_API_KEY sk-or-...
```

### Optional API Keys

| Feature | Provider | Env Variable |
|---|---|---|
| Web scraping | Firecrawl | `FIRECRAWL_API_KEY` |
| Browser automation | Browserbase | `BROWSERBASE_API_KEY`, `BROWSERBASE_PROJECT_ID` |
| Image generation | FAL | `FAL_KEY` |
| Premium TTS | ElevenLabs | `ELEVENLABS_API_KEY` |
| OpenAI TTS + STT | OpenAI | `VOICE_TOOLS_OPENAI_KEY` |
| RL Training | Tinker + WandB | `TINKER_API_KEY`, `WANDB_API_KEY` |

---



## Messaging Gateway

Chat with Arachne from Telegram, Discord, Slack, or WhatsApp. The gateway is a single background process that connects to all configured platforms, handles sessions, runs cron jobs, and delivers voice messages.

```bash
arachne gateway              # Run in foreground
arachne gateway install      # Install as systemd service (Linux)
arachne gateway start        # Start the systemd service
arachne gateway stop         # Stop the systemd service
arachne gateway status       # Check service status
```

### Telegram Setup

1. Create a bot via [@BotFather](https://t.me/BotFather)
2. Get your user ID via [@userinfobot](https://t.me/userinfobot)
3. Configure:
```bash
# ~/.arachne/.env
TELEGRAM_BOT_TOKEN=123456:ABC-DEF...
TELEGRAM_ALLOWED_USERS=YOUR_USER_ID
```
4. `arachne gateway`

### Discord Setup

1. Create a bot at the [Discord Developer Portal](https://discord.com/developers/applications)
2. Enable Message Content Intent under Bot → Privileged Gateway Intents
3. Invite with scopes `bot` + `applications.commands` and Send Messages / Read Message History permissions
4. Configure:
```bash
# ~/.arachne/.env
DISCORD_BOT_TOKEN=MTIz...
DISCORD_ALLOWED_USERS=YOUR_USER_ID
```

### Slack Setup

1. Create an app at [api.slack.com/apps](https://api.slack.com/apps) with Socket Mode enabled
2. Configure:
```bash
# ~/.arachne/.env
SLACK_BOT_TOKEN=xoxb-...
SLACK_APP_TOKEN=xapp-...
SLACK_ALLOWED_USERS=U01234ABCDE
```

### WhatsApp Setup

**Option A — WhatsApp Business API** (requires Meta Business verification)

**Option B — whatsapp-web.js bridge** (personal accounts):
```bash
# ~/.arachne/.env
WHATSAPP_ENABLED=true
WHATSAPP_ALLOWED_USERS=15551234567
```
On first launch the gateway displays a QR code — scan with WhatsApp to link the session.

### Gateway Commands (inside chat)

| Command | Description |
|---|---|
| `/new` or `/reset` | Start fresh conversation |
| `/model [name]` | Show or change the model |
| `/personality [name]` | Set a personality |
| `/retry` | Retry the last message |
| `/undo` | Remove the last exchange |
| `/status` | Show session info |
| `/stop` | Stop the running agent |
| `/sethome` | Set this chat as the home channel |
| `/help` | Show available commands |

### DM Pairing

Instead of manually configuring user ID allowlists, use pairing codes:

```bash
# Unknown user DMs the bot, receives: "Pairing code: XKGH5N7P"
arachne pairing approve telegram XKGH5N7P
arachne pairing list
arachne pairing revoke telegram 123456789
```

### Security

By default the gateway denies all users not in an allowlist or paired via DM.

```bash
# Restrict to specific users (recommended)
TELEGRAM_ALLOWED_USERS=123456789,987654321

# Explicitly allow all users (NOT recommended)
GATEWAY_ALLOW_ALL_USERS=true
```

---

## Commands

```bash
# Chat
arachne                     # Interactive chat
arachne chat -q "Hello"     # Single query

# Provider & model
arachne model               # Switch provider and model interactively
arachne login               # Authenticate with Nous Portal
arachne logout              # Clear OAuth credentials

# Configuration
arachne setup               # Full setup wizard
arachne config              # View/edit configuration
arachne config check        # Check for missing config
arachne config migrate      # Add missing options interactively
arachne status              # Show configuration status
arachne doctor              # Diagnose issues

# Maintenance
arachne update              # Update to latest version
arachne uninstall           # Uninstall

# Gateway
arachne gateway             # Run gateway in foreground
arachne gateway install     # Install as system service
arachne gateway status      # Check service status

# Skills, cron, misc
arachne skills search k8s   # Search skill registries
arachne skills install ...  # Install a skill
arachne skills list         # List installed skills
arachne cron list           # View scheduled jobs
arachne cron status         # Check if scheduler is running
arachne pairing list        # View/manage DM pairing codes
arachne version             # Show version info
```

### CLI Commands (inside chat)

| Command | Description |
|---|---|
| `/help` | Show available commands |
| `/tools` | List available tools |
| `/toolsets` | List available toolsets |
| `/model [name]` | Show or change model |
| `/prompt` | View/set custom system prompt |
| `/personality [name]` | Set personality |
| `/clear` | Clear screen and reset conversation |
| `/history` | Show conversation history |
| `/reset` | Reset conversation (keep screen) |
| `/retry` | Retry the last message |
| `/undo` | Remove the last exchange |
| `/save` | Save the current conversation |
| `/config` | Show current configuration |
| `/cron` | Manage scheduled tasks |
| `/skills` | Search, install, inspect, manage skills |
| `/platforms` | Show gateway/messaging platform status |
| `/quit` | Exit |

**Keybindings:**
- `Enter` — send message
- `Alt+Enter` or `Ctrl+J` — new line
- `Ctrl+C` — interrupt agent (double-press to force exit)
- `Ctrl+D` — exit

---

## Features

### Tools & Toolsets

Tools are organized into logical toolsets:

```bash
arachne --toolsets "web,terminal"   # Use specific toolsets
arachne --list-tools                # List all toolsets
```

Available toolsets: `web`, `terminal`, `file`, `browser`, `vision`, `image_gen`, `moa`, `skills`, `tts`, `todo`, `memory`, `session_search`, `cronjob`, `code_execution`, `delegation`, `clarify`, and more.

### Terminal & Process Management

| Backend | Description | Use Case |
|---|---|---|
| `local` | Run on your machine (default) | Development, trusted tasks |
| `docker` | Isolated containers | Security, reproducibility |
| `ssh` | Remote server | Sandboxing, production |
| `singularity` | HPC containers | Cluster computing, rootless |
| `modal` | Cloud execution | Serverless, scale |

Configure in `~/.arachne/config.yaml`:
```yaml
terminal:
  backend: local    # or: docker, ssh, singularity, modal
  cwd: "."
  timeout: 180
```

**Background processes:** Start with `terminal(command="...", background=true)`, then use `process(action="poll/wait/log/kill/write")` to manage. PTY mode (`pty=true`) enables interactive CLI tools.

### Persistent Memory

- **MEMORY.md** — agent's personal notes. ~800 token budget.
- **USER.md** — user profile and preferences. ~500 token budget.

Both are injected into the system prompt at session start. The agent manages its own memory via the `memory` tool.

```yaml
memory:
  memory_enabled: true
  user_profile_enabled: true
  memory_char_limit: 2200
  user_char_limit: 1375
```

### Context Files

| File | Purpose |
|---|---|
| `AGENTS.md` | Project-specific instructions, coding conventions |
| `SOUL.md` | Persona definition |
| `.cursorrules` / `.cursor/rules/*.mdc` | Cursor IDE rules (also detected) |

Arachne picks these up automatically from the working directory. `AGENTS.md` is hierarchical. All context files are capped at 20,000 characters.

### Context Compression

Long conversations are automatically summarized when approaching context limits:

```yaml
compression:
  enabled: true
  threshold: 0.85    # Compress at 85% of limit
```

### Session Store

All CLI and messaging sessions are stored in a SQLite database (`~/.arachne/state.db`) with FTS5 full-text search, compression-triggered session splitting, and source tagging (cli, telegram, discord, etc.).

### Scheduled Tasks (Cron)

```bash
/cron add 30m "Remind me to check the build"
/cron add "0 9 * * *" "Morning briefing"
/cron list
/cron remove <job_id>
```

Cron execution is handled by the gateway daemon. The gateway ticks every 60 seconds; a file lock prevents duplicate execution.

```bash
arachne gateway install    # Install as system service
arachne cron list          # View scheduled jobs
arachne cron status        # Check if gateway is running
```

### Exec Approval (Messaging Platforms)

When Arachne tries to run a potentially dangerous command on Telegram/Discord/WhatsApp, it asks for approval before executing:

> ⚠️ This command is potentially dangerous (recursive delete). Reply "yes" to approve.


### Text-to-Speech

| Provider | Quality | Cost | API Key |
|---|---|---|---|
| **Edge TTS** (default) | Good | Free | None |
| **ElevenLabs** | Excellent | Paid | `ELEVENLABS_API_KEY` |
| **OpenAI TTS** | Good | Paid | `OPENAI_API_KEY` |

On Telegram, audio plays as native voice bubbles. Requires `ffmpeg` for Edge TTS → Opus conversion.

### Voice Message Transcription

Voice messages on Telegram, Discord, WhatsApp, or Slack are automatically transcribed via OpenAI Whisper and injected as text. Requires `OPENAI_API_KEY`.

```yaml
stt:
  enabled: true
  model: "whisper-1"
```

### Browser Automation

```bash
arachne config set BROWSERBASE_API_KEY your_key
arachne config set BROWSERBASE_PROJECT_ID your_project_id
cd ~/.arachne/arachne && npm install
```

Available tools: `browser_navigate`, `browser_snapshot`, `browser_click`, `browser_type`, `browser_scroll`, `browser_back`, `browser_press`, `browser_close`, `browser_get_images`.

### Skills System

Skills are on-demand knowledge documents compatible with the [agentskills.io](https://agentskills.io/specification) open standard. All skills live in `~/.arachne/skills/`.

```bash
arachne skills search kubernetes
arachne skills install openai/skills/k8s
arachne skills inspect openai/skills/k8s
arachne skills list
arachne skills uninstall k8s
```

The agent creates and manages its own skills via the `skill_manage` tool — procedural memory that accumulates the longer Arachne runs.

### Code Execution (Programmatic Tool Calling)

The `execute_code` tool lets Arachne write Python scripts that call tools programmatically, collapsing multi-step workflows into a single LLM turn. Intermediate results never enter the context window.

```python
from arachne_tools import web_search, web_extract
results = web_search("topic", limit=5)
for r in results["data"]["web"]:
    content = web_extract([r["url"]])
    # filter and process...
print(summary)
```

```yaml
code_execution:
  timeout: 300
  max_tool_calls: 50
```

### Subagents (Task Delegation)

```python
# Single task
delegate_task(goal="Debug failing tests", toolsets=["terminal", "file"])

# Parallel batch
delegate_task(tasks=[
    {"goal": "Research topic A", "toolsets": ["web"]},
    {"goal": "Research topic B", "toolsets": ["web"]},
])
```

Each subagent gets isolated context and its own terminal session. Depth limit of 2. Interrupting the parent interrupts all active children.

### RL Training (Tinker + Atropos)

> ⚠️ In Development — RL training integration is not yet functional.

Train models with reinforcement learning using the Tinker API and Atropos framework.

```bash
# Required keys in ~/.arachne/.env
TINKER_API_KEY=your-tinker-key
WANDB_API_KEY=your-wandb-key
```

Available tools: `rl_list_environments`, `rl_select_environment`, `rl_get_current_config`, `rl_edit_config`, `rl_test_inference`, `rl_start_training`, `rl_check_status`, `rl_stop_training`, `rl_get_results`, `rl_list_runs`.

---

## Manual Installation

### Prerequisites

| Requirement | Minimum | Check |
|---|---|---|
| Git | Any recent | `git --version` |
| Node.js | 18+ | `node --version` |
| ripgrep | Any | `rg --version` |

Python is not a prerequisite — uv provisions Python 3.11 automatically.

```bash
# Ubuntu/Debian
sudo apt update && sudo apt install git ripgrep nodejs npm

# macOS
brew install git ripgrep node
```

### Step 1: Clone

```bash
git clone --recurse-submodules https://github.com/trevoraspencer/arachne.git
cd arachne
```

### Step 2: Install uv & Create Venv

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv venv venv --python 3.11
```

### Step 3: Install Python Dependencies

```bash
export VIRTUAL_ENV="$(pwd)/venv"
uv pip install -e ".[all]"
```

| Extra | What it adds |
|---|---|
| `all` | Everything |
| `messaging` | Telegram & Discord gateway |
| `cron` | Cron expression parsing |
| `cli` | Terminal menu UI |
| `modal` | Modal cloud backend |
| `dev` | pytest & test utilities |

### Step 4: Install Submodule Packages

```bash
uv pip install -e "./mini-swe-agent"
uv pip install -e "./tinker-atropos"
```

### Step 5: Node Dependencies (Optional — browser tools only)

```bash
npm install
```

### Step 6: Create Configuration Directory

```bash
mkdir -p ~/.arachne/{cron,sessions,logs,memories,skills}
cp cli-config.yaml.example ~/.arachne/config.yaml
touch ~/.arachne/.env
```

### Step 7: Add API Keys

```bash
# ~/.arachne/.env
OPENROUTER_API_KEY=sk-or-v1-your-key

# Optional
FIRECRAWL_API_KEY=fc-your-key
BROWSERBASE_API_KEY=bb-your-key
BROWSERBASE_PROJECT_ID=your-project-id
FAL_KEY=your-fal-key
TELEGRAM_BOT_TOKEN=123456:ABC-DEF
TELEGRAM_ALLOWED_USERS=your-user-id
```

### Step 8: Add `arachne` to PATH

```bash
mkdir -p ~/.local/bin
ln -sf "$(pwd)/venv/bin/arachne" ~/.local/bin/arachne
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### Step 9: Verify

```bash
arachne version
arachne doctor
arachne status
arachne chat -q "Hello"
```

### Condensed

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
git clone --recurse-submodules https://github.com/trevoraspencer/arachne.git
cd arachne
uv venv venv --python 3.11
export VIRTUAL_ENV="$(pwd)/venv"
uv pip install -e ".[all]"
uv pip install -e "./mini-swe-agent"
uv pip install -e "./tinker-atropos"
npm install
mkdir -p ~/.arachne/{cron,sessions,logs,memories,skills}
cp cli-config.yaml.example ~/.arachne/config.yaml
echo 'OPENROUTER_API_KEY=sk-or-v1-your-key' >> ~/.arachne/.env
mkdir -p ~/.local/bin
ln -sf "$(pwd)/venv/bin/arachne" ~/.local/bin/arachne
arachne doctor
arachne
```

### Manual Update

```bash
cd /path/to/arachne
export VIRTUAL_ENV="$(pwd)/venv"
git pull origin main
git submodule update --init --recursive
uv pip install -e ".[all]"
uv pip install -e "./mini-swe-agent"
uv pip install -e "./tinker-atropos"
arachne config check
arachne config migrate
```

---

## Batch Processing

```bash
python batch_runner.py \
  --dataset_file=prompts.jsonl \
  --batch_size=20 \
  --run_name=my_run \
  --num_workers=4 \
  --distribution=default
```

| Flag | Description |
|---|---|
| `--dataset_file` | JSONL file with prompts |
| `--batch_size` | Prompts per batch |
| `--run_name` | Name for output/checkpoints |
| `--num_workers` | Parallel workers (default: 4) |
| `--distribution` | Toolset distribution |
| `--resume` | Resume from checkpoint |

Output: `data/<run_name>/trajectories.jsonl`

### Trajectory Compression

```bash
python trajectory_compressor.py --input=data/my_run
python trajectory_compressor.py --input=data/my_run --sample_percent=15
python trajectory_compressor.py --input=data/my_run --target_max_tokens=16000
```

---

## Python API

```python
from run_agent import AIAgent

agent = AIAgent(
    model="anthropic/claude-sonnet-4",
    enabled_toolsets=["web", "terminal"]
)

result = agent.run_conversation("Search for the latest Python news")
print(result["final_response"])
```

---

## Environment Variables Reference

All variables go in `~/.arachne/.env`. Use `arachne config set VAR value` to set them.

**LLM Providers:**

| Variable | Description |
|---|---|
| `OPENROUTER_API_KEY` | OpenRouter API key |
| `ANTHROPIC_API_KEY` | Direct Anthropic access |
| `OPENAI_API_KEY` | Custom OpenAI-compatible endpoint key |
| `OPENAI_BASE_URL` | Custom endpoint URL |
| `VOICE_TOOLS_OPENAI_KEY` | OpenAI key for TTS and STT |

**Tool APIs:**

| Variable | Description |
|---|---|
| `FIRECRAWL_API_KEY` | Web scraping |
| `BROWSERBASE_API_KEY` | Browser automation |
| `BROWSERBASE_PROJECT_ID` | Browserbase project |
| `FAL_KEY` | Image generation |

**Terminal Backend:**

| Variable | Description |
|---|---|
| `TERMINAL_ENV` | Backend: `local`, `docker`, `ssh`, `singularity`, `modal` |
| `TERMINAL_DOCKER_IMAGE` | Docker image |
| `TERMINAL_SINGULARITY_IMAGE` | Singularity image or `.sif` path |
| `TERMINAL_TIMEOUT` | Command timeout in seconds |
| `TERMINAL_CWD` | Working directory |
| `SUDO_PASSWORD` | Enable sudo (plaintext — use with care) |
| `TERMINAL_SSH_HOST` | SSH server hostname |
| `TERMINAL_SSH_USER` | SSH username |
| `TERMINAL_SSH_PORT` | SSH port (default: 22) |
| `TERMINAL_SSH_KEY` | Path to private key |

**Container Resources:**

| Variable | Description |
|---|---|
| `TERMINAL_CONTAINER_CPU` | CPU cores (default: 1) |
| `TERMINAL_CONTAINER_MEMORY` | Memory in MB (default: 5120) |
| `TERMINAL_CONTAINER_DISK` | Disk in MB (default: 51200) |
| `TERMINAL_CONTAINER_PERSISTENT` | Persist filesystem across sessions (default: true) |
| `TERMINAL_SANDBOX_DIR` | Host directory for sandboxes (default: `~/.arachne/sandboxes/`) |

**Messaging:**

| Variable | Description |
|---|---|
| `TELEGRAM_BOT_TOKEN` | Telegram bot token |
| `TELEGRAM_ALLOWED_USERS` | Comma-separated user IDs |
| `TELEGRAM_HOME_CHANNEL` | Default channel for cron delivery |
| `DISCORD_BOT_TOKEN` | Discord bot token |
| `DISCORD_ALLOWED_USERS` | Comma-separated user IDs |
| `DISCORD_HOME_CHANNEL` | Default cron delivery channel |
| `MESSAGING_CWD` | Terminal working directory for messaging sessions |
| `GATEWAY_ALLOW_ALL_USERS` | Allow all users without allowlist (default: false) |

**Agent Behavior:**

| Variable | Description |
|---|---|
| `ARACHNE_MAX_ITERATIONS` | Max tool-calling iterations per conversation (default: 60) |
| `ARACHNE_TOOL_PROGRESS` | Send progress messages during tool use |
| `ARACHNE_TOOL_PROGRESS_MODE` | `all` or `new` |
| `CONTEXT_COMPRESSION_ENABLED` | Auto-compression (default: true) |
| `CONTEXT_COMPRESSION_THRESHOLD` | Trigger threshold (default: 0.85) |
| `CONTEXT_COMPRESSION_MODEL` | Model for summaries |

---

## File Structure

| Path | Description |
|---|---|
| `~/.arachne/config.yaml` | Settings |
| `~/.arachne/.env` | API keys and secrets |
| `~/.arachne/auth.json` | OAuth credentials |
| `~/.arachne/skills/` | All active skills |
| `~/.arachne/sandboxes/` | Docker/Singularity persistent workspaces |
| `agent/` | Agent internals (context compressor, prompt builder, display) |
| `arachne_cli/` | CLI implementation |
| `tools/` | Tool implementations and central registry (`tools/registry.py`) |
| `tools/environments/` | Terminal backends (local, docker, ssh, singularity, modal) |
| `tools/approval.py` | Dangerous command detection and approval state |
| `arachne_constants.py` | Constants |
| `arachne_state.py` | State management |
| `skills/` | Bundled skill sources (copied to `~/.arachne/skills/` on install) |
| `gateway/` | Messaging platform adapters |
| `cron/` | Scheduler implementation |

---

## Troubleshooting

```bash
arachne doctor    # Run diagnostics
arachne status    # Check configuration
arachne config    # View current settings
```

Common issues:

- **"API key not set"** — Run `arachne setup` or `arachne config set OPENROUTER_API_KEY your_key`
- **"arachne: command not found"** — Reload your shell or check PATH
- **"Run `arachne login` to re-authenticate"** — Nous Portal session expired
- **Gateway won't start** — Check `arachne gateway status` and logs
- **Missing config after update** — Run `arachne config check`, then `arachne config migrate`

---

## Upstream Sync

To pull upstream changes selectively:

```bash
git fetch upstream
git log upstream/main --oneline   # Review changes before merging
git cherry-pick <commit>          # Apply specific commits
```

When reviewing upstream changes, check for any new `hermes` references introduced and rename them before committing.

---

## License

MIT — see [LICENSE](LICENSE) for details.

Forked from [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) under MIT. All upstream model name strings preserved as-is.
