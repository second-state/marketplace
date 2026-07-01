# Second State 插件市场

[English](README.md)

这个仓库发布 Second State 面向 Claude Code 和 Codex 的插件。

当前可用插件：

| 插件 | 说明 |
| --- | --- |
| `vibekeys` | VibeKeys BLE 控制器，用于向 BLE 键盘发送消息和状态 |

插件市场名称是 `second-state-tools`。

## 选择正确的安装方式

`vibekeys` CLI 是核心控制器。它通过 BLE 与 VibeKeys MAX 设备通信，也可以单独使用。

如果你只是想手动控制设备，只需要安装 CLI。先阅读
[安装 VibeKeys CLI](#安装-vibekeys-cli)，然后使用这些命令：

```bash
vibekeys send "Hello"
vibekeys keymap ESC Ctrl+C
vibekeys wifi-config "MyWiFi" --pass "password"
vibekeys asr-config
```

如果你想让 Claude Code 或 Codex 自动把 agent 状态显示到键盘屏幕上，需要安装两部分：

1. 安装控制器：[安装 VibeKeys CLI](#安装-vibekeys-cli)。
2. 安装对应的 agent 集成：[在 Claude Code 中安装](#在-claude-code-中安装) 或
   [在 Codex 中安装](#在-codex-中安装)。

| 使用场景 | 需要 `vibekeys` CLI | 需要插件 |
| --- | --- | --- |
| 手动向键盘发送消息 | 是 | 否 |
| 手动配置按键绑定 | 是 | 否 |
| 手动配置 WiFi 或 ASR | 是 | 否 |
| 自动显示 Claude Code 状态 | 是 | 是 |
| 自动显示 Codex 状态 | 是 | 是 |

简而言之：CLI 提供设备控制能力；插件把 Claude Code 或 Codex 的事件连接到这个 CLI。

## 这个插件做什么

`vibekeys` 插件把 Claude Code 或 Codex 连接到本机的 VibeKeys 控制器。安装后，agent 事件可以显示到 VibeKeys MAX 键盘屏幕上，包括用户提示、工具调用、通知和完成状态。

这个插件使用
[second-state/vibekeys_app](https://github.com/second-state/vibekeys_app)
提供的 `vibekeys` 命令行工具。安装插件不需要阅读那个仓库，但你需要先安装 `vibekeys` binary，并确保 VibeKeys MAX 设备已开机且在附近。

## 安装 VibeKeys CLI

使用插件前，请先安装 CLI。

macOS ARM64：

```bash
wget https://github.com/second-state/vibekeys_app/releases/latest/download/vibekeys-macos-arm64
chmod +x vibekeys-macos-arm64
sudo mv vibekeys-macos-arm64 /usr/local/bin/vibekeys
```

Linux x64：

```bash
wget https://github.com/second-state/vibekeys_app/releases/latest/download/vibekeys-linux-x64
chmod +x vibekeys-linux-x64
sudo mv vibekeys-linux-x64 /usr/local/bin/vibekeys
```

Windows PowerShell：

```powershell
Invoke-WebRequest -Uri "https://github.com/second-state/vibekeys_app/releases/latest/download/vibekeys-windows-x64.exe" -OutFile "vibekeys.exe"
# 将 vibekeys.exe 加入 PATH，或移动到 PATH 中的目录。
```

## 在 Claude Code 中安装

添加插件市场：

```bash
claude plugin marketplace add second-state/marketplace
```

安装 VibeKeys 插件：

```bash
claude plugin install vibekeys@second-state-tools
```

安装后重启 Claude Code，让插件在下一个 session 中加载。

你也可以在 Claude Code 内使用 slash commands 执行同样的步骤：

```text
/plugin marketplace add second-state/marketplace
/plugin install vibekeys@second-state-tools
```

## 在 Codex 中安装

添加插件市场：

```bash
codex plugin marketplace add second-state/marketplace
```

安装 VibeKeys 插件：

```bash
codex plugin add vibekeys@second-state-tools
```

安装后重启 Codex，让插件在下一个 session 中加载。

## 验证

Claude Code：

```bash
claude plugin list
```

Codex：

```bash
codex plugin list
```

在插件列表中确认能看到 `vibekeys`。

## 使用插件

安装 CLI 和插件后，重启 Claude Code 或 Codex。插件 hooks 会在 agent 事件发生时自动调用 `vibekeys`。

你也可以向键盘屏幕发送一条测试消息：

```bash
vibekeys send "Hello from VibeKeys"
```

### 切换按键映射 profile

使用 profile 在 Claude Code 和 Codex 的 VibeKeys MAX 按键配置之间切换：

```bash
vibekeys profile codex    # 把键盘配置成 Codex 用法
vibekeys profile claude   # 切回 Claude Code
```

profile 会用一条命令套用一组预设按键绑定。两个 profile 都只修改 `CUSTOM` 和
`YOLO`，因此来回切换不会影响你的其他按键绑定。

| Profile | `CUSTOM` | `YOLO` |
| --- | --- | --- |
| `codex` | 输入 `/review` + 回车，用于一键代码审查 | 输入 `y`，用于批准 |
| `claude` | 输入 `/compact` + 回车 | 发送 `Shift+Tab`，用于允许全部编辑 |

### 配置按键绑定

把 VibeKeys MAX 按键映射到快捷键：

```bash
vibekeys keymap ESC Ctrl+C
vibekeys keymap NEXT Ctrl+Shift+P
vibekeys keymap CUSTOM Alt+Tab
vibekeys keymap ROTATE Cmd+Space
```

把按键映射到文本宏：

```bash
vibekeys keymap MIC '"I am using Claude Code"'
```

支持的设备按键：

这些是 VibeKeys MAX 设备上可以重新映射的按键。键帽标识是你在键盘上实际看到的文字。`YOLO` 是 CLI 里的别名，对应设备里的 `SWITCH` 键。

| CLI 按键名 | 键帽标识 | 示例 |
| --- | --- | --- |
| `MIC` | `MIC` | `vibekeys keymap MIC '"Start voice input"'` |
| `CUSTOM` | `CUSTOM` | `vibekeys keymap CUSTOM Alt+Tab` |
| `ESC` | `ESC` | `vibekeys keymap ESC Ctrl+C` |
| `NEXT` | `NEXT` | `vibekeys keymap NEXT Ctrl+Shift+P` |
| `BACKSPACE` | `BACKSPACE` | `vibekeys keymap BACKSPACE Backspace` |
| `YOLO` | `YOLO` | `vibekeys keymap YOLO '"y"'` |
| `ACCEPT` | `ACCEPT` | `vibekeys keymap ACCEPT Enter` |
| `ROTATE` | `ROTATE` | `vibekeys keymap ROTATE Cmd+Space` |

### 配置语音和 WiFi

为语音功能配置 ASR：

```bash
vibekeys asr-config
```

配置 WiFi：

```bash
vibekeys wifi-config "MyWiFi" --pass "mypassword"
```

开放网络：

```bash
vibekeys wifi-config "PublicWiFi"
```

## 源码和开发

大多数用户只需要阅读这个插件市场 README。

实际的 VibeKeys CLI 和插件实现位于
[second-state/vibekeys_app](https://github.com/second-state/vibekeys_app)：

- CLI 源码和 releases：`vibekeys_app`
- 这个 marketplace 使用的插件源码：`vibekeys_app/vibekeys_plugin`
- Claude Code hooks：`vibekeys_plugin/hooks/hooks.json`
- Codex hooks：`vibekeys_plugin/hooks/codex-hooks.json`

## 更新

Claude Code：

```bash
claude plugin marketplace update second-state-tools
claude plugin update vibekeys
```

Codex：

```bash
codex plugin marketplace upgrade second-state-tools
codex plugin remove vibekeys@second-state-tools
codex plugin add vibekeys@second-state-tools
```

## 卸载

Claude Code：

```bash
claude plugin uninstall vibekeys
```

Codex：

```bash
codex plugin remove vibekeys@second-state-tools
```
