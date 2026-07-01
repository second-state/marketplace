# Second State Plugin Marketplace

[中文文档](README.zh.md)

This repository publishes Second State plugins for Claude Code and Codex.

Available plugin:

| Plugin | Description |
| --- | --- |
| `vibekeys` | VibeKeys BLE controller for sending messages and status to a BLE keyboard |

The marketplace name is `second-state-tools`.

## Choose the right setup

The `vibekeys` CLI is the core controller. It talks to the VibeKeys MAX device
over BLE and can be used by itself.

For manual device control, install only the CLI. Start with
[Install the VibeKeys CLI](#install-the-vibekeys-cli), then use commands such as:

```bash
vibekeys send "Hello"
vibekeys keymap ESC Ctrl+C
vibekeys wifi-config "MyWiFi" --pass "password"
vibekeys asr-config
```

To show Claude Code or Codex status updates on the keyboard automatically, install
both parts:

1. Install the controller: [Install the VibeKeys CLI](#install-the-vibekeys-cli).
2. Install the integration for your agent: [Install in Claude Code](#install-in-claude-code)
   or [Install in Codex](#install-in-codex).

| Use case | Need `vibekeys` CLI | Need plugin |
| --- | --- | --- |
| Send a message to the keyboard manually | Yes | No |
| Configure key bindings manually | Yes | No |
| Configure WiFi or ASR manually | Yes | No |
| Show Claude Code status automatically | Yes | Yes |
| Show Codex status automatically | Yes | Yes |

In short: the CLI provides the device control; the plugin connects Claude Code or
Codex events to that CLI.

## What this plugin does

The `vibekeys` plugin connects Claude Code or Codex to the local VibeKeys controller.
After installation, agent events can be sent to the VibeKeys MAX keyboard display,
including prompts, tool use, notifications, and completion status.

The plugin uses the `vibekeys` command-line tool from
[second-state/vibekeys_app](https://github.com/second-state/vibekeys_app).
You do not need to read that repository to install the plugin, but you do need the
`vibekeys` binary installed and the VibeKeys MAX device powered on nearby.

## Install the VibeKeys CLI

Install the CLI before using the plugin.

macOS ARM64:

```bash
wget https://github.com/second-state/vibekeys_app/releases/latest/download/vibekeys-macos-arm64
chmod +x vibekeys-macos-arm64
sudo mv vibekeys-macos-arm64 /usr/local/bin/vibekeys
```

Linux x64:

```bash
wget https://github.com/second-state/vibekeys_app/releases/latest/download/vibekeys-linux-x64
chmod +x vibekeys-linux-x64
sudo mv vibekeys-linux-x64 /usr/local/bin/vibekeys
```

Windows PowerShell:

```powershell
Invoke-WebRequest -Uri "https://github.com/second-state/vibekeys_app/releases/latest/download/vibekeys-windows-x64.exe" -OutFile "vibekeys.exe"
# Add vibekeys.exe to PATH or move it to a directory in PATH.
```

## Install in Claude Code

Add the marketplace:

```bash
claude plugin marketplace add second-state/marketplace
```

Install the VibeKeys plugin:

```bash
claude plugin install vibekeys@second-state-tools
```

Restart Claude Code after installation so the plugin is loaded in the next session.

You can also run the same steps from inside Claude Code with slash commands:

```text
/plugin marketplace add second-state/marketplace
/plugin install vibekeys@second-state-tools
```

## Install in Codex

Add the marketplace:

```bash
codex plugin marketplace add second-state/marketplace
```

Install the VibeKeys plugin:

```bash
codex plugin add vibekeys@second-state-tools
```

Restart Codex after installation so the plugin is loaded in the next session.

## Verify

Claude Code:

```bash
claude plugin list
```

Codex:

```bash
codex plugin list
```

Look for `vibekeys` in the installed plugin list.

## Use the plugin

After installing the CLI and plugin, restart Claude Code or Codex. The plugin hooks
will call `vibekeys` automatically when agent events happen.

You can also send a test message to the keyboard display:

```bash
vibekeys send "Hello from VibeKeys"
```

### Switch keymap profiles

Use a profile to switch the VibeKeys MAX keymap between Claude Code and Codex:

```bash
vibekeys profile codex    # set up the keyboard for Codex
vibekeys profile claude   # switch back to Claude Code
```

Profiles apply a predefined set of key bindings in one command. Both profiles
only change `CUSTOM` and `YOLO`, so switching between them does not affect your
other key bindings.

| Profile | `CUSTOM` | `YOLO` |
| --- | --- | --- |
| `codex` | Types `/review` + Enter for one-key code review | Types `y` to approve |
| `claude` | Types `/compact` + Enter | Sends `Shift+Tab` to allow all edits |

### Configure key bindings

Map a VibeKeys MAX key to a shortcut:

```bash
vibekeys keymap ESC Ctrl+C
vibekeys keymap NEXT Ctrl+Shift+P
vibekeys keymap CUSTOM Alt+Tab
vibekeys keymap ROTATE Cmd+Space
```

Map a key to a text macro:

```bash
vibekeys keymap MIC '"I am using Claude Code"'
```

Supported device keys:

These are VibeKeys MAX device keys that can be remapped. The keycap label is
what you see printed on the keyboard. `YOLO` is the CLI alias for the device
`SWITCH` key.

| CLI key | Keycap label | Example |
| --- | --- | --- |
| `MIC` | `MIC` | `vibekeys keymap MIC '"Start voice input"'` |
| `CUSTOM` | `CUSTOM` | `vibekeys keymap CUSTOM Alt+Tab` |
| `ESC` | `ESC` | `vibekeys keymap ESC Ctrl+C` |
| `NEXT` | `NEXT` | `vibekeys keymap NEXT Ctrl+Shift+P` |
| `BACKSPACE` | `BACKSPACE` | `vibekeys keymap BACKSPACE Backspace` |
| `YOLO` | `YOLO` | `vibekeys keymap YOLO '"y"'` |
| `ACCEPT` | `ACCEPT` | `vibekeys keymap ACCEPT Enter` |
| `ROTATE` | `ROTATE` | `vibekeys keymap ROTATE Cmd+Space` |

### Configure voice and WiFi

Configure ASR for voice features:

```bash
vibekeys asr-config
```

Configure WiFi:

```bash
vibekeys wifi-config "MyWiFi" --pass "mypassword"
```

Open network:

```bash
vibekeys wifi-config "PublicWiFi"
```

## Source and development

Most users only need this marketplace README.

The actual VibeKeys CLI and plugin implementation live in
[second-state/vibekeys_app](https://github.com/second-state/vibekeys_app):

- CLI source and releases: `vibekeys_app`
- Plugin source used by this marketplace: `vibekeys_app/vibekeys_plugin`
- Claude Code hooks: `vibekeys_plugin/hooks/hooks.json`
- Codex hooks: `vibekeys_plugin/hooks/codex-hooks.json`

## Update

Claude Code:

```bash
claude plugin marketplace update second-state-tools
claude plugin update vibekeys
```

Codex:

```bash
codex plugin marketplace upgrade second-state-tools
codex plugin remove vibekeys@second-state-tools
codex plugin add vibekeys@second-state-tools
```

## Uninstall

Claude Code:

```bash
claude plugin uninstall vibekeys
```

Codex:

```bash
codex plugin remove vibekeys@second-state-tools
```

## License

MIT
