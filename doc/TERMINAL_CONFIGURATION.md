# Terminal Configuration

TRCC can be configured entirely from a terminal. The graphical interface is
not required. The headless `trccd.service` daemon owns the USB device and
continues running across Desktop Mode → Gamescope/Gaming Mode transitions.

## Use the daemon

The daemon is installed and enabled by the Bazzite installer. Confirm it is
running:

```bash
systemctl --user status trccd.service
trcc daemon-status
```

For commands issued through the persistent daemon, set:

```bash
export TRCC_DAEMON=1
```

This makes terminal commands use the daemon's existing USB connection instead
of creating a second process that could compete for the device.

## Find the device

```bash
trcc device list
trcc device state 0416:8001
```

The Thermalright LED controller used by this setup is normally:

```text
0416:8001
```

## Persistent LED settings

The following commands update saved settings under `~/.trcc`:

```bash
trcc led mode 0416:8001 rainbow
trcc led color 0416:8001 '#00ffff'
trcc led brightness 0416:8001 65
trcc led toggle 0416:8001 on
```

Supported modes include `static`, `breathing`, `colorful`, `rainbow`,
`temp_linked`, and `load_linked`.

Inspect the saved state:

```bash
trcc led snapshot 0416:8001
trcc led snapshot 0416:8001 --json
```

Zone-specific settings are also persistent when supported by the controller:

```bash
trcc led zone-color 0416:8001 0 '#ff0000'
trcc led zone-mode 0416:8001 0 breathing
trcc led zone-brightness 0416:8001 0 50
```

## Persistent LCD settings

`0416:8001` is an LED controller. LCD panels supported by TRCC can be
configured with commands such as:

```bash
trcc display load-theme DEVICE_KEY /path/to/theme
trcc display set-brightness DEVICE_KEY 75
trcc display set-orientation DEVICE_KEY 90
trcc display overlay DEVICE_KEY on
```

Reload the saved LCD theme after a restart or suspend if needed:

```bash
trcc display resume
```

`load-theme` persists the selected theme. One-shot commands such as
`send-image` and `display color` are intended for temporary output and do not
replace the saved theme.

## Global sensor settings

These settings are also persistent:

```bash
trcc config temp-unit C
trcc config refresh-interval 2
trcc config gpu ''
```

## Verify after a restart

```bash
systemctl --user restart trccd.service
sleep 3
trcc daemon-status
trcc device list
trcc led snapshot 0416:8001
```

The daemon service is deliberately not attached to
`graphical-session.target`. Do not re-enable the old GUI autostart entry or a
second TRCC process, because either can take ownership of the controller and
make the display appear to stop updating.

