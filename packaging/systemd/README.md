# trccd service files

The TRCC daemon is the recommended Linux/Bazzite runtime. It owns USB,
polls sensors, and renders LED state without starting Qt or any graphical
interface. It is independent of `graphical-session.target`, so it survives
Desktop Mode → Gamescope/Gaming Mode transitions.

## Linux (systemd user unit)

```bash
# Install the unit (one-time; from this repository)
mkdir -p ~/.config/systemd/user
cp trccd.service ~/.config/systemd/user/

# Enable + start
systemctl --user daemon-reload
systemctl --user enable --now trccd.service

# Check status
systemctl --user status trccd

# Tail logs
journalctl --user -u trccd -f
```

To uninstall:

```bash
systemctl --user disable --now trccd.service
rm ~/.config/systemd/user/trccd.service
systemctl --user daemon-reload
```

## macOS (LaunchAgent)

```bash
cp ../launchd/com.thermalright.trccd.plist ~/Library/LaunchAgents/
launchctl load ~/Library/LaunchAgents/com.thermalright.trccd.plist
```

To uninstall:

```bash
launchctl unload ~/Library/LaunchAgents/com.thermalright.trccd.plist
rm ~/Library/LaunchAgents/com.thermalright.trccd.plist
```

## Windows

A scheduled task is installed by `trcc system setup` (Phase 11 wires this in).
Manual install:

```powershell
schtasks /Create /SC ONLOGON /TN "TRCC Daemon" /TR "trcc daemon" /F
```

## Verifying

After install, in any terminal:

```bash
# Should print info about the running daemon (or auto-spawn one)
trcc detect

# The IPC socket should exist:
ls -la $XDG_RUNTIME_DIR/trcc-linux.sock      # Linux
ls -la /tmp/trcc-linux.sock                  # macOS / fallback
```
