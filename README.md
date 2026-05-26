# ha-awtrix-claude-usage

![Home Assistant Blueprint](https://img.shields.io/badge/Home%20Assistant-Blueprint-blue)
![Awtrix 3](https://img.shields.io/badge/Awtrix-3-green)
![MQTT](https://img.shields.io/badge/integration-MQTT-orange)
![HA 2024.6+](https://img.shields.io/badge/HA-2024.6%2B-lightgrey)

A Home Assistant blueprint that displays your **Claude AI usage** as a live progress bar on an Awtrix LED matrix. Usage percentage scrolls with color-coded alerts - green when you're within limits, yellow when approaching them, red when close to the cap.

---

## Prerequisites

- Home Assistant 2024.6 or newer
- [Awtrix 3](https://blueforcer.github.io/awtrix3) with MQTT enabled
- MQTT broker (e.g. Mosquitto add-on)
- A Home Assistant sensor exposing Claude usage as a percentage (0–100), e.g. via [hass-claude-usage](https://github.com/trickv/hass-claude-usage)
- (optional) A Claude icon installed on your Awtrix device - see [Awtrix icon docs](https://blueforcer.github.io/awtrix3/#/icons)


---

## Installation

### One-click import

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/tnusch/ha-awtrix-claude-usage/main/blueprints/awtrix_claude_usage.yaml)

Or copy the URL below into **Settings → Automations → Blueprints → Import Blueprint**:

```
https://raw.githubusercontent.com/tnusch/ha-awtrix-claude-usage/main/blueprints/awtrix_claude_usage.yaml
```

---

## Configuration

| Parameter | Default | Description |
|---|---|---|
| `usage_sensor` | *(required)* | Sensor entity providing Claude usage as a percentage (0–100) |
| `awtrix` | *(required)* | Your Awtrix 3 device (selected from device registry) |
| `app_name` | `claude_usage` | Unique MQTT app name on the Awtrix |
| `icon_id` | `74725` | Awtrix icon ID for Claude |
| `suffix_text` | `wk` | Suffix shown after the percentage, e.g. `wk` for weekly, `se` for session |
| `base_color` | `DE7356` | Hex color (no `#`) for text and progress bar - default is Claude brand color |
| `threshold_warn` | `50` | Usage % above which text turns yellow |
| `threshold_high` | `80` | Usage % above which text turns red |
| `scroll_speed` | `100` | Scroll speed as a percentage of the original speed |
| `duration` | `5` | How long the app is displayed in seconds |
| `text_case` | Use global setting | Controls uppercase/as-entered text rendering |
| `push_icon` | Icon doesn't move | Controls icon scroll behavior |

---

## Usage tips
 
This blueprint can be instantiated **multiple times** - once per automation. A common setup is to run it twice: once for your weekly usage and once for your session usage, each pointing to a different sensor and using a unique `app_name` so the two apps coexist on the Awtrix display rotation.
 
Example:
 
| Automation | Sensor | `app_name` | `suffix_text` |
|---|---|---|---|
| Claude Weekly | `sensor.claude_usage_weekly` | `claude_weekly` | `wk` |
| Claude Session | `sensor.claude_usage_session` | `claude_session` | `se` |
 

---
 
## How it works
 
1. Triggers immediately on HA start and whenever the usage sensor changes (with a 5-second debounce)
2. Reads the usage sensor with a safe `| int(-1)` fallback for unavailable states
3. Resolves the Awtrix device name from the device registry and publishes JSON to `<device>/custom/<app_name>` via MQTT
4. Awtrix displays `67% wk` with a progress bar and color-coded text
5. Shows `N/A` with a neutral progress bar if the sensor is unavailable

---

## Display preview

```
[icon]  67% wk  [████████░░]
```

Color coding:

| Usage | Color |
|---|---|
| ≤ 50% | 🟢 Green |
| 51–80% | 🟡 Yellow |
| > 80% | 🔴 Red |

Thresholds are fully configurable in the blueprint inputs.

---

PRs and issues welcome. If you build related Awtrix blueprints (other API usage meters, productivity displays, etc.) feel free to open a PR to add them.
