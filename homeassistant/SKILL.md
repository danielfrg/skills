---
name: homeassistant
description: |
  Control Home Assistant smart home devices using the hass-cli via uv.

  Use for:
  - Listing and inspecting entity states (lights, switches, sensors, etc.)
  - Turning devices on/off, toggling, setting brightness/temperature
  - Calling any Home Assistant service
  - Querying state history
  - Listing devices, areas, and entities
  - Monitoring events
  - Checking system health and logs

  Requires HASS_SERVER and HASS_TOKEN environment variables to be set.
---

# Home Assistant CLI

Control Home Assistant devices and services via `hass-cli`, run through `uv`.

## Usage

Use uv to run the mdvector command.

Use the --with flag to specify the dependency:

```bash
uv run --with homeassistant-cli hass-cli [OPTIONS] COMMAND
```

## Global Options

| Flag             | Description                                                            |
| ---------------- | ---------------------------------------------------------------------- |
| `-o json`        | JSON output                                                            |
| `-o yaml`        | YAML output                                                            |
| `-o table`       | Table output (default)                                                 |
| `--no-headers`   | Omit table headers (useful for scripting)                              |
| `--sort-by TEXT` | Sort table by jsonpath (e.g. `last_changed`)                           |
| `--columns TEXT` | Custom columns (e.g. `ENTITY=entity_id,NAME=attributes.friendly_name`) |

## Commands Reference

### State (entity states)

```bash
# List all entity states
uv run --with homeassistant-cli hass-cli state list

# Filter by domain
uv run --with homeassistant-cli hass-cli state list light
uv run --with homeassistant-cli hass-cli state list switch
uv run --with homeassistant-cli hass-cli state list sensor
uv run --with homeassistant-cli hass-cli state list climate

# Get detailed state for one entity
uv run --with homeassistant-cli hass-cli -o yaml state get light.living_room

# Turn on / turn off / toggle
uv run --with homeassistant-cli hass-cli state turn_on light.living_room
uv run --with homeassistant-cli hass-cli state turn_off light.living_room
uv run --with homeassistant-cli hass-cli state toggle light.living_room

# State history (relative time: 30m, 2h, 1d, etc.)
uv run --with homeassistant-cli hass-cli state history --since 1h light.living_room
uv run --with homeassistant-cli hass-cli state history --since 30m --end now sensor.temperature
```

### Services

```bash
# List all services
uv run --with homeassistant-cli hass-cli service list

# Filter services by domain
uv run --with homeassistant-cli hass-cli service list light

# Get service details
uv run --with homeassistant-cli hass-cli -o yaml service list light.turn_on

# Call a service with arguments
uv run --with homeassistant-cli hass-cli service call light.turn_on --arguments entity_id=light.living_room
uv run --with homeassistant-cli hass-cli service call light.turn_on --arguments entity_id=light.bedroom,brightness=128
uv run --with homeassistant-cli hass-cli service call climate.set_temperature --arguments entity_id=climate.thermostat,temperature=22
uv run --with homeassistant-cli hass-cli service call scene.turn_on --arguments entity_id=scene.movie_time
uv run --with homeassistant-cli hass-cli service call homeassistant.toggle --arguments entity_id=switch.fan
```

### Devices

```bash
# List all devices
uv run --with homeassistant-cli hass-cli device list

# Assign device to an area
uv run --with homeassistant-cli hass-cli device assign DEVICE_ID AREA_ID

# Rename a device
uv run --with homeassistant-cli hass-cli device rename DEVICE_ID "New Name"
```

### Entities

```bash
# List all entities
uv run --with homeassistant-cli hass-cli entity list

# Assign entity to an area
uv run --with homeassistant-cli hass-cli entity assign ENTITY_ID AREA_ID

# Rename an entity
uv run --with homeassistant-cli hass-cli entity rename ENTITY_ID "New Name"
```

### Areas

```bash
# List all areas
uv run --with homeassistant-cli hass-cli area list

# Create / rename / delete an area
uv run --with homeassistant-cli hass-cli area create "Office"
uv run --with homeassistant-cli hass-cli area rename AREA_ID "Home Office"
uv run --with homeassistant-cli hass-cli area delete AREA_ID
```

### Events

```bash
# Watch all events in real-time
uv run --with homeassistant-cli hass-cli event watch

# Watch specific event types
uv run --with homeassistant-cli hass-cli event watch state_changed
uv run --with homeassistant-cli hass-cli event watch automation_triggered

# Fire a custom event
uv run --with homeassistant-cli hass-cli event fire MY_EVENT
```

### System

```bash
# System health check
uv run --with homeassistant-cli hass-cli system health

# View error logs
uv run --with homeassistant-cli hass-cli system log

# Get HA config
uv run --with homeassistant-cli hass-cli config

# Get HA info
uv run --with homeassistant-cli hass-cli info
```

### Templates

```bash
# Render a Jinja2 template on the server
uv run --with homeassistant-cli hass-cli template template.j2

# Render locally
uv run --with homeassistant-cli hass-cli template --local template.j2 data.yaml
```

### Raw API

```bash
# GET request
uv run --with homeassistant-cli hass-cli raw get /api/states

# POST request
uv run --with homeassistant-cli hass-cli raw post /api/services/light/turn_on '{"entity_id": "light.living_room"}'

# WebSocket request
uv run --with homeassistant-cli hass-cli raw ws '{"type": "get_states"}'
```

## Tips

- Use `state list <domain>` to discover entity IDs before calling services.
- Use `-o json` when you need to parse output programmatically.
- History supports relative times: `30m`, `2h`, `1d`, `3 days`, etc.
- The `--arguments` flag takes comma-separated key=value pairs.
- `service list <domain>` shows available services and their parameters.
