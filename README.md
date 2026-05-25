# ha-awtrix-claude-usage

![Home Assistant Blueprint](https://img.shields.io/badge/Home%20Assistant-Blueprint-blue)
![Awtrix 3](https://img.shields.io/badge/Awtrix-3-green)
![MQTT](https://img.shields.io/badge/integration-MQTT-orange)
![HA 2024.6+](https://img.shields.io/badge/HA-2024.6%2B-lightgrey)

A Home Assistant blueprint that displays your **weekly Claude AI usage** as a live progress bar on an Awtrix LED matrix. Usage percentage scrolls with color-coded alerts - green when you're within limits, yellow when approaching them, red when close to the cap.

---

## Prerequisites

- Home Assistant 2024.6 or newer
- [Awtrix 3](https://blueforcer.github.io/awtrix3) with MQTT enabled
- MQTT broker (e.g. Mosquitto add-on)
- A Home Assistant sensor exposing your weekly Claude usage as a percentage (0–100)

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
| `usage_sensor` | *(required)* | Sensor entity providing weekly Claude usage (0–100) |
| `awtrix_topic` | `awtrix_ed5874` | MQTT topic prefix of your Awtrix device |
| `icon_id` | `74725` | Awtrix icon ID for Claude |
| `base_color` | `DE7356` | Hex color (no `#`) for text and progress bar |
| `threshold_warn` | `50` | Usage % above which text turns yellow |
| `threshold_high` | `80` | Usage % above which text turns red |
| `update_interval` | `1` | Refresh interval in hours (1–12) |

---

## How it works

1. Triggers on HA start and every *N* hours (configurable)
2. Reads the usage sensor with a safe `| int(-1)` fallback for unavailable states
3. Publishes JSON to `<prefix>/custom/claude_usage` via MQTT
4. Awtrix displays `67% wk` with a progress bar and color-coded text
5. Shows `N/A` in gray if the sensor is unavailable

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

## Repository structure

```
ha-awtrix-claude-usage/
└── blueprints/
    └── awtrix_claude_usage.yaml
└── README.md
```

---

## Contributing

PRs and issues welcome. If you build related Awtrix blueprints (other API usage meters, productivity displays, etc.) feel free to open a PR to add them.

---

## License

MIT
