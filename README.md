# AWTRIX NG Battery Monitoring Blueprint 🔋

A Home Assistant automation blueprint for displaying real-time home battery status on AWTRIX NG devices with intelligent mode detection based on power flow.

## Features

- 📊 **Real-time Battery Display** — Shows current charge percentage with dynamic icons
- 🔄 **Automatic Mode Detection** — Intelligently determines charging, discharging, or idle state from power sensor
- ⚙️ **Configurable Idle Thresholds** — Define custom power thresholds for idle state (e.g., -10W to +4W)
- 📈 **Progress Bar Support** — Optional visual progress bar for charge level
- 🎯 **Multi-Device Support** — Deploy to multiple AWTRIX NG devices simultaneously
- 🔔 **Automatic Updates** — Refreshes on charge/power changes and periodically every 5 minutes
- 🎨 **Mode-Specific Icons** — Different battery icons for charging, discharging, and idle states

## Requirements

- **Home Assistant** 2024.11 or newer
- **AWTRIX NG** device with:
  - MQTT integration enabled
  - Home Assistant discovery enabled
- **Battery Sensors**:
  - Battery state of charge (0-100%)
  - Battery power in watts (negative = charging, positive = discharging)

## Installation

1. Copy `batt_monit_ng.yaml` to your Home Assistant blueprints directory:
   ```bash
   ~/.homeassistant/blueprints/automation/
   ```

2. Reload automation blueprints in Home Assistant or restart

3. Go to **Settings → Automations & Scenes → Create Automation → Create from Blueprint**

4. Search for "AWTRIX NG Battery Monitoring" and select it

## Configuration

### Required Settings

- **AWTRIX NG devices** — Select one or more target devices
- **Battery charge level sensor** — Entity providing charge % (0-100)
- **Battery power sensor** — Entity providing power in watts
  - Negative values = charging state
  - Positive values = discharging state

### Display Behavior

- **App name** (default: `battery`) — Custom name for the app on AWTRIX
- **Idle charging threshold** (default: `-10W`) — Power level below which charging is considered idle
- **Idle discharging threshold** (default: `+4W`) — Power level above which discharging is considered idle
- **Show progress bar** (default: enabled) — Display charge level as progress bar
- **Display refresh rate** (default: `1`) — How many times the display refreshes per update (1-10)

### Mode Detection Logic

The blueprint calculates battery mode from power values:

| Power Range | Mode |
|-------------|------|
| ≤ -10W | **Charging** (fast charge) |
| -10W to +4W | **Idle** (minimal power flow) |
| ≥ +4W | **Discharging** (supplying power) |

Adjust thresholds to match your system's power characteristics.

## Icon Mapping

Icons automatically select based on mode and charge level:

### Charging Mode (negative power)
- >85% → ⚡ (32047)
- >69% → ⚡ (32049)
- >51% → ⚡ (32050)
- >34% → ⚡ (32051)
- >17% → ⚡ (32052)
- ≤17% → ⚡ (32053)

### Discharging Mode (positive power)
- >85% → 🔋 (32059)
- >69% → 🔋 (32058)
- >51% → 🔋 (32057)
- >34% → 🔋 (32056)
- >17% → 🔋 (32055)
- ≤17% → 🔋 (32054)

### Idle Mode
- >90% → 🔋 (13735)
- >70% → 🔋 (13732)
- >40% → 🔋 (13731)
- >10% → 🔋 (13725)
- ≤10% → 🔋 (13734)

## Example Payload

The blueprint publishes MQTT messages to your AWTRIX NG device in this format:

```json
{
  "text": "87 %",
  "icon": 32047,
  "progress": 87,
  "repeat": 1
}
```

## Triggers

The automation activates on:

- ✅ Battery charge level changes
- ✅ Battery power value changes (mode detection)
- ✅ Home Assistant startup
- ✅ Periodic refresh every 5 minutes
- ✅ AWTRIX device goes online (if matrix entity available)

## AWTRIX NG Migration

This blueprint is designed for **AWTRIX NG firmware**. If you're migrating from AWTRIX 3, refer to the official migration guide:

> https://blueforcer.github.io/awtrix-ng/guides/migrating-from-awtrix3/

## Origin & Credits

This blueprint is derived from the battery monitoring flow at:

> https://flows.blueforcer.de/flow/QE6B5QWRoMq0

The original concept has been adapted and enhanced for the new AWTRIX NG firmware and Home Assistant blueprint system, with added features including:
- Power-based mode detection with configurable idle thresholds
- Blueprint-driven configuration for easier deployment
- Multi-device support via Home Assistant device discovery

## Troubleshooting

### Display not updating

- Verify battery sensors are available and have recent values
- Check MQTT connectivity to AWTRIX device
- Ensure app name doesn't conflict with other apps

### Wrong mode detected

- Adjust idle thresholds to match your battery system's behavior
- Example: If your system shows -5W at idle, lower the charge threshold to -5W

### Icons not displaying

- Verify icon numbers correspond to your AWTRIX firmware version
- Consult AWTRIX NG icon documentation

## License

This blueprint is provided as-is. Feel free to modify and adapt for your needs.

## Support

For issues related to:
- **AWTRIX NG firmware**: https://github.com/blueforcer/awtrix-ng
- **Home Assistant blueprints**: https://www.home-assistant.io/docs/automation/using_blueprints/
- **MQTT in Home Assistant**: https://www.home-assistant.io/integrations/mqtt/
