---
name: discord-cli
description: "Discord operations via CLI with daemon mode for fast execution. Send messages, read channels, list guilds and threads with persistent connection and auto-restart on code changes."
---

# Discord CLI Skill

Perform Discord operations via CLI commands with daemon mode for instant responses.

## Architecture

This skill uses a **client-daemon architecture**:
- **Daemon** (`scripts/daemon.py`): Maintains persistent Discord connection, auto-restarts on code changes
- **Client** (`scripts/dcli.py`): Sends commands to daemon via Unix socket

Benefits:
- **Instant execution**: No WebSocket connection overhead per command
- **Auto-restart**: Daemon automatically restarts when code changes
- **Process management**: Built-in start/stop/restart/status commands

## Prerequisites

- Ensure `.env` file exists in skill root with: `DISCORD_TOKEN=your_token`
- Dependencies must be installed (see Installation section)
- Daemon will auto-start on first command if not already running

## Installation

### First-time Setup (or environment not initialized)

```bash
# Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate  # Linux/Mac
# or: venv\Scripts\activate  # Windows

# Install dependencies
pip install -r requirements.txt

# Create .env file (add your Discord Token)
echo "DISCORD_TOKEN=your_token_here" > .env
```

### Already Configured (daily use)

If virtual environment and dependencies are already installed, just activate the environment:

```bash
source venv/bin/activate  # Linux/Mac
# or: venv\Scripts\activate  # Windows
```

## Usage

All commands are executed via local `scripts/dcli.py`:

### Daemon Management

```bash
# Check status
python3 scripts/dcli.py daemon status

# Start Daemon
python3 scripts/dcli.py daemon start

# Stop Daemon
python3 scripts/dcli.py daemon stop

# Restart Daemon
python3 scripts/dcli.py daemon restart
```

**Note**: The daemon auto-starts when you run any command if it's not already running.

### Discord Commands

#### Send Message
```bash
python3 scripts/dcli.py send-message --channel CHANNEL_ID --content "Hello World"
```

#### Read Messages
```bash
python3 scripts/dcli.py read-messages --channel CHANNEL_ID --limit 20
```

#### List Guilds (Servers)
```bash
python3 scripts/dcli.py list-guilds
```

#### List Channels in Guild
```bash
python3 scripts/dcli.py list-channels --guild GUILD_ID
```

#### List All Threads in Guild
```bash
python3 scripts/dcli.py list-guild-threads --guild GUILD_ID
```

#### Get User Info
```bash
# Get current user info
python3 scripts/dcli.py user-info

# Get specific user info
python3 scripts/dcli.py user-info --user USER_ID
```

#### List Threads in Channel
```bash
# Active threads only
python3 scripts/dcli.py list-threads --channel CHANNEL_ID

# Include archived threads
python3 scripts/dcli.py list-threads --channel CHANNEL_ID --archived
```

#### Read Thread Messages
```bash
python3 scripts/dcli.py read-thread --thread THREAD_ID --limit 50
```

## Configuration

The skill reads `DISCORD_TOKEN` from `.env` file in the skill root directory.

Create a `.env` file:
```bash
DISCORD_TOKEN=your_discord_token_here
```

> **Important:** Automating user accounts is against the Discord ToS. Use this at your own risk.

## Notes

- **Daemon Mode**: Uses persistent connection for instant command execution
- **Auto-Restart**: Daemon monitors its own code and restarts automatically when changes are detected
- **Process Management**: Built-in commands to manage the daemon lifecycle
- **Socket Communication**: Client and daemon communicate via Unix socket at `/tmp/discord-cli-daemon.sock`
- **Rate Limiting**: Respected automatically by the underlying discord.py library

## Troubleshooting

**Daemon not responding?**
```bash
# Check if daemon is running
python3 scripts/dcli.py daemon status

# Restart daemon
python3 scripts/dcli.py daemon restart
```

**Socket errors?**
```bash
# Clean up and restart
python3 scripts/dcli.py daemon stop
rm /tmp/discord-cli-daemon.sock
python3 scripts/dcli.py daemon start
```

**Python not found?**
Ensure Python 3.10+ is installed and available in PATH.

## Related

- [Main Repository](https://github.com/Microck/discord.py-self-mcp)
- [MCP Documentation](./README.md)
- [discord.py-self](https://github.com/dolfies/discord.py-self) - Underlying library
