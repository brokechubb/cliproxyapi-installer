# CLIProxyAPI Linux Installer

A comprehensive Linux installation script for [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) that automates installation, upgrades, and management of the CLIProxyAPI service.

## Features

- 🚀 **Automatic Installation** - Detects your Linux architecture and downloads the latest version
- 🔄 **Smart Upgrades** - Preserves your configuration and automatically manages systemd service during upgrades
- 🔑 **API Key Management** - Automatically generates secure API keys
- 🛡️ **Systemd Service** - Creates and manages systemd service files with proper lifecycle management
- 📊 **Status Monitoring** - Check installation status and configuration
- 🧹 **Cleanup** - Automatically removes old versions (keeps latest 2)
- 📚 **Documentation Management** - Built-in documentation tools
- ⚡ **Zero-Downtime Updates** - Service is properly stopped and restarted during upgrades

## Quick Start

### Install CLIProxyAPI

```bash
# Download and run the installer
curl -fsSL https://raw.githubusercontent.com/brokechubb/cliproxyapi-installer/refs/heads/master/cliproxyapi-installer | bash

# Or clone and run manually
git clone https://github.com/brokechubb/cliproxyapi-installer.git
cd cliproxyapi-installer
./cliproxyapi-installer
```

### After Installation

1. **Configure API keys** (if not automatically generated):
   ```bash
   cd ~/cliproxyapi
   nano config.yaml
   ```

2. **Set up authentication** (choose one or more):
   ```bash
   ./cli-proxy-api --login           # For Gemini
   ./cli-proxy-api --codex-login     # For OpenAI
   ./cli-proxy-api --claude-login    # For Claude
   ./cli-proxy-api --qwen-login      # For Qwen
   ./cli-proxy-api --iflow-login     # For iFlow
   ```

3. **Start the service**:
     ```bash
     # Direct execution
     ./cli-proxy-api

     # Or as a systemd service (recommended)
     systemctl --user enable cliproxyapi.service
     systemctl --user start cliproxyapi.service
     systemctl --user status cliproxyapi.service
     ```

4. **Enable autostart on boot** (recommended):
     ```bash
     # Enable the service to start automatically on user login
     systemctl --user enable cliproxyapi.service
     
     # Verify it's enabled
     systemctl --user is-enabled cliproxyapi.service
     ```

> **💡 Pro Tip**: The installer automatically manages the systemd service during upgrades. If the service is running when you upgrade, it will be gracefully stopped, updated, and restarted automatically.

## Usage

The installer script supports multiple commands:

```bash
./cliproxyapi-installer [COMMAND]
```

### Commands

| Command | Description |
|---------|-------------|
| `install` / `upgrade` | Install or upgrade CLIProxyAPI (default) |
| `status` | Show current installation status |
| `auth` | Display authentication setup information |
| `check-config` | Verify configuration and API keys |
| `generate-key` | Generate a new API key |
| `manage-docs` | Manage documentation and check consistency |
| `uninstall` | Remove CLIProxyAPI completely |
| `-h` / `--help` | Show help message |

### Examples

```bash
# Install or upgrade to the latest version
./cliproxyapi-installer

# Check current installation status
./cliproxyapi-installer status

# Verify your configuration
./cliproxyapi-installer check-config

# Generate a new API key
./cliproxyapi-installer generate-key

# Show authentication setup info
./cliproxyapi-installer auth

# Uninstall completely
./cliproxyapi-installer uninstall
```

## Configuration

### Installation Directory
CLIProxyAPI is installed to `~/cliproxyapi/` with the following structure:
```
~/cliproxyapi/
├── cli-proxy-api          # Main executable
├── config.yaml            # Configuration file
├── cliproxyapi.service    # Systemd service file
├── version.txt            # Current version info
├── x.x.x/                 # Version-specific directory
└── config_backup/         # Configuration backups
```

### API Keys

The installer automatically generates secure API keys in OpenAI format (`sk-...`). These keys are used for authenticating requests to your proxy server, **not** for provider authentication.

To view or modify your API keys:
```bash
cd ~/cliproxyapi
nano config.yaml
```

### Authentication Providers

CLIProxyAPI supports multiple AI providers:

- **Gemini (Google)**: `./cli-proxy-api --login`
- **OpenAI (Codex/GPT)**: `./cli-proxy-api --codex-login`
- **Claude (Anthropic)**: `./cli-proxy-api --claude-login`
- **Qwen (Qwen Chat)**: `./cli-proxy-api --qwen-login`
- **iFlow**: `./cli-proxy-api --iflow-login`

Add `--no-browser` to any login command to print the URL instead of opening a browser automatically.

## System Requirements

- **Operating System**: Linux (amd64, arm64)
- **Required Tools**: `curl` or `wget`, `tar`
- **Shell**: Bash

### Installing Dependencies

**Ubuntu/Debian:**
```bash
sudo apt-get install curl wget tar
```

**CentOS/RHEL:**
```bash
sudo yum install curl wget tar
```

**Fedora:**
```bash
sudo dnf install curl wget tar
```

## Systemd Service

The installer creates and manages a systemd service file for easy lifecycle management:

### ✨ Smart Service Management

The installer provides intelligent service handling during upgrades:

- **Automatic Detection**: Detects if the service is running before upgrades
- **Graceful Shutdown**: Safely stops the service before applying updates
- **Auto-Restart**: Restarts the service after successful upgrades
- **State Preservation**: Maintains the service's previous running state

### Basic Service Management

```bash
# Enable the service (starts on user login)
systemctl --user enable cliproxyapi.service

# Start the service
systemctl --user start cliproxyapi.service

# Check service status
systemctl --user status cliproxyapi.service

# View service logs
journalctl --user -u cliproxyapi.service -f

# Stop the service
systemctl --user stop cliproxyapi.service

# Restart the service
systemctl --user restart cliproxyapi.service
```

### Service Status During Upgrades

When you run `./cliproxyapi-installer upgrade`, the installer will:

1. **Check** if the service is currently running
2. **Stop** the service gracefully if it's active
3. **Apply** the upgrade (download, extract, update files)
4. **Restart** the service if it was running before
5. **Report** the final service status

You'll see output like:
```
[INFO] Service is currently running and will be restarted after upgrade
[INFO] Stopping CLIProxyAPI service...
[SUCCESS] Service stopped
...
[INFO] Restarting CLIProxyAPI service...
[SUCCESS] Service restarted successfully
```

### Autostart Configuration

**To enable CLIProxyAPI to start automatically on system boot:**

```bash
# Enable the service for automatic startup on user login
systemctl --user enable cliproxyapi.service

# Verify the service is enabled
systemctl --user is-enabled cliproxyapi.service

# Check if the service will start on boot
systemctl --user is-active cliproxyapi.service
```

**To disable autostart:**
```bash
systemctl --user disable cliproxyapi.service
```

**Important Notes:**
- The `--user` flag means the service runs as your user and starts when you log in
- For system-wide startup (requires root), you would need to manually install the service file to `/etc/systemd/system/`
- User services require lingering to be enabled for startup without login: `loginctl enable-linger $USER`

**If the service is not working:**
```bash
# Reload systemd daemon
systemctl --user daemon-reload

# Check service status for errors
systemctl --user status cliproxyapi.service

# View detailed logs
journalctl --user -u cliproxyapi.service -n 50

# Check if service file exists
ls -la ~/.config/systemd/user/cliproxyapi.service
```

## Troubleshooting

### Common Issues

1. **Permission Denied**
    ```bash
    chmod +x cliproxyapi-installer
    ```

2. **Missing Dependencies**
    ```bash
    # Check what's missing
    ./cliproxyapi-installer status
    
    # Install required tools
    sudo apt-get install curl wget tar  # Ubuntu/Debian
    ```

3. **API Keys Not Configured**
    ```bash
    ./cliproxyapi-installer check-config
    # Follow the instructions to configure API keys
    ```

4. **Service Won't Start**
    ```bash
    # Check service logs
    journalctl --user -u cliproxyapi.service -n 50
    
    # Check configuration
    ./cliproxyapi-installer check-config
    ```

5. **Port Already in Use**
    ```bash
    # Check what's using port 8317
    netstat -tlnp | grep 8317
    
    # Stop the existing process
    pkill cli-proxy-api
    
    # Then restart the service
    systemctl --user restart cliproxyapi.service
    ```

6. **Systemd Service Issues**
    ```bash
    # Reload systemd daemon
    systemctl --user daemon-reload
    
    # Check if service file exists
    ls -la ~/.config/systemd/user/cliproxyapi.service
    
    # Reset service (disable and re-enable)
    systemctl --user disable cliproxyapi.service
    systemctl --user enable cliproxyapi.service
    systemctl --user start cliproxyapi.service
    ```

7. **Upgrade Service Issues**
    ```bash
    # If service doesn't restart after upgrade
    systemctl --user status cliproxyapi.service
    
    # Check recent service logs
    journalctl --user -u cliproxyapi.service -n 20
    
    # Manually restart if needed
    systemctl --user restart cliproxyapi.service
    ```

8. **Configuration Protection Issues**
    ```bash
    # If your config was accidentally overwritten (should never happen)
    # Check backup directory
    ls -la ~/cliproxyapi/config_backup/
    
    # Restore from latest backup
    cp ~/cliproxyapi/config_backup/config_YYYYMMDD_HHMMSS.yaml ~/cliproxyapi/config.yaml
    
    # Restart service after restoring
    systemctl --user restart cliproxyapi.service
    ```

### Getting Help

```bash
# Show all available commands
./cliproxyapi-installer --help

# Check installation status
./cliproxyapi-installer status

# Verify configuration
./cliproxyapi-installer check-config
```

## Security Considerations

- API keys are automatically generated using cryptographically secure random strings
- Configuration files are stored in your home directory with standard permissions
- The systemd service runs with appropriate security restrictions
- Backups of configuration are created automatically during upgrades
- **User configurations are never overwritten** - your modifications are protected during upgrades

## Updates and Upgrades

The installer automatically checks for newer versions:

```bash
# Check for updates and upgrade if available
./cliproxyapi-installer upgrade

# Or simply run (upgrade is the default action)
./cliproxyapi-installer
```

### Smart Upgrade Process

During upgrades, the installer provides intelligent service management:

- **🔄 Service Management**: If the service is running, it's automatically stopped before upgrade and restarted afterward
- **🛡️ Configuration Protection**: Your `config.yaml` file is **never overwritten** - user modifications are preserved
- **💾 Automatic Backups**: Configuration backups are created automatically before any changes
- **🧹 Version Cleanup**: Old versions are cleaned up (latest 2 versions kept)
- **📋 Service Updates**: Systemd service file is updated if needed

### Upgrade Behavior

| Scenario | Service Action | Config Action |
|----------|----------------|---------------|
| Service running | Stop → Upgrade → Restart | Preserved with backup |
| Service stopped | Upgrade only | Preserved with backup |
| First install | N/A | Created from example with generated keys |

> **🔒 Your configuration is safe**: The installer uses a priority system that always preserves existing user configurations over example files.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

This installer script is released under the same license as CLIProxyAPI.

## Support

- **CLIProxyAPI Documentation**: https://github.com/router-for-me/CLIProxyAPI
- **Installer Issues**: https://github.com/brokechubb/cliproxyapi-installer/issues
- **General Help**: Run `./cliproxyapi-installer --help`

## Changelog

### Recent Improvements

#### ✅ **Smart Service Management**
- **Automatic Service Detection**: Installer detects if CLIProxyAPI service is running before upgrades
- **Graceful Service Handling**: Service is properly stopped before upgrade and restarted afterward
- **State Preservation**: Service maintains its previous running state after upgrades
- **Enhanced Logging**: Clear feedback about service status throughout the upgrade process

#### ✅ **Enhanced Configuration Protection**
- **Never Overwrite**: User-modified `config.yaml` files are never replaced during upgrades
- **Priority System**: Clear hierarchy for configuration preservation (backup → existing → previous → example)
- **Automatic Backups**: Configuration backups created before any upgrade operations
- **User Notifications**: Clear messaging when user configurations are preserved

#### ✅ **Improved Systemd Integration**
- **Fixed Service File**: Resolved systemd service configuration issues
- **Better Error Handling**: Improved service startup and restart reliability
- **Simplified Security**: Removed problematic restrictions while maintaining security

---

**Note**: This installer is specifically for Linux systems. For other operating systems, please refer to the main CLIProxyAPI repository.
