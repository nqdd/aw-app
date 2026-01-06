activity-watch
==============
[![Build](https://github.com/nqdd/activity-watch/actions/workflows/build.yml/badge.svg)](https://github.com/nqdd/activity-watch/actions/workflows/build.yml)

A cross-platform ActivityWatch desktop application built with [Tauri 2.x](https://tauri.app/).

This is the modern replacement for aw-qt, providing a streamlined way to run ActivityWatch on all platforms.

## Features

 - **System Tray**: Full-featured tray icon with module management
 - **Module Manager**: Automatic discovery, start/stop, and crash recovery for watchers
 - **Embedded Server**: Runs aw-server-rust as part of the main executable
 - **WebView**: Serves the aw-webui directly within the app
 - **Single Instance**: Prevents multiple instances from running
 - **Autostart**: Configurable system autostart with minimized launch option
 - **Notifications**: Native system notifications via aw-notify integration
 - **Sync Support**: Built-in aw-sync daemon support
 - **Linux Wayland**: Auto-detection and aw-awatcher support
 - **Log Rotation**: Automatic log file rotation (32MB max, 5 rotated files)
 - **TOML Configuration**: User-configurable settings for port, modules, and discovery paths

## Benefits of Tauri 2.x

 - Builds cross-platform nicely (see [their docs](https://tauri.app/start/prerequisites/))
   - Generates deb and AppImage with a simple `npx tauri build`
   - Uses Gtk on Linux, and [tao](https://github.com/tauri-apps/tao) on Windows and macOS
   - No more messy PyInstaller for the main entrypoint (aw-qt)
   - Good [docs for code-signing](https://tauri.app/distribute/) on all platforms
   - Includes an [updater](https://tauri.app/plugin/updater/) for `MSI`, `.AppImage`, `.app` bundle
 - Contains a webview with an easy interface to Rust code
 - [Tray icon support](https://tauri.app/learn/system-tray/)
 - Mobile support available for iOS and Android

## Prerequisites

 - Tauri 2.x dependencies (see [their docs](https://tauri.app/start/prerequisites/))
 - Node.js (v20.19.0 or compatible, see `.nvmrc`)
 - Rust (Edition 2021)
 - Python 3 with Poetry (for building watchers)

## Usage

Clone with submodules:

```sh
git clone --recursive https://github.com/nqdd/activity-watch.git
cd activity-watch
```

To run in development mode:

```sh
npm install
make dev
```

To build:

```sh
make build
```

### Available Make Commands

| Command | Description |
|---------|-------------|
| `make build` | Full production build with prebuild step |
| `make dev` | Development mode with hot reload |
| `make prebuild` | Build aw-webui, install watchers, setup modules |
| `make install-watchers` | Install both afk and window watchers |
| `make install-sync` | Build aw-sync |
| `make modules` | Create modules directory with symlinks |
| `make package` | Package built artifacts (platform-specific) |
| `make format` | Format Rust code |
| `make check` | Run cargo check and clippy |

## Project Structure

```
activity-watch/
├── aw-app/                  # Tauri 2.x application (Rust)
│   ├── src/
│   │   ├── main.rs          # Entry point
│   │   ├── lib.rs           # Core app logic, server, tray
│   │   ├── manager.rs       # Module process manager
│   │   ├── dirs.rs          # Platform-specific directories
│   │   └── logging.rs       # Log rotation setup
│   ├── tauri.conf.json      # Tauri configuration
│   └── capabilities/        # Tauri 2.0 security capabilities
├── aw-webui/                # Web UI (git submodule, Vue 2.7)
├── aw-server-rust/          # Server backend (git submodule)
├── aw-watcher-afk/          # AFK watcher (git submodule, Python)
├── aw-watcher-window/       # Window watcher (git submodule, Python)
├── aw-notify/               # Notification binary
├── modules/                 # Symlinks to built module executables
├── venv/                    # Python virtual environment
└── Makefile                 # Build commands
```

## Configuration

Configuration file location (TOML format):
- **Linux**: `~/.config/activitywatch/aw-app/config.toml`
- **macOS**: `~/Library/Application Support/activitywatch/aw-app/config.toml`
- **Windows**: `%APPDATA%\activitywatch\aw-app\config.toml`

Example configuration:

```toml
port = 5600

[autostart]
enabled = true
minimized = true
modules = ["aw-watcher-afk", "aw-watcher-window"]

# Module with custom arguments
[autostart.modules.aw-sync]
args = ["daemon"]
```

## Log Files

Log files with rotation (32MB max, 5 rotated files):
- **Linux**: `~/.cache/activitywatch/aw-app/log/aw-app.log`
- **macOS**: `~/Library/Logs/activitywatch/aw-app/aw-app.log`
- **Windows**: `%LOCALAPPDATA%\activitywatch\aw-app\log\aw-app.log`

## Submodules

| Submodule | Description |
|-----------|-------------|
| [aw-webui](https://github.com/ActivityWatch/aw-webui) | Vue 2.7 web interface |
| [aw-server-rust](https://github.com/ActivityWatch/aw-server-rust) | Rust server backend |
| [aw-watcher-window](https://github.com/ActivityWatch/aw-watcher-window) | Window activity watcher |
| [aw-watcher-afk](https://github.com/ActivityWatch/aw-watcher-afk) | AFK detection watcher |

## Supported Platforms

- macOS (x64, ARM)
- Linux (x64, ARM) - X11 and Wayland
- Windows (x64)
