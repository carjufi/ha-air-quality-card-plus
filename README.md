# Air Quality Card Plus

A custom Home Assistant Lovelace card for indoor and outdoor air-quality dashboards, with compact gradient graphs and health-oriented status thresholds.

This is a personalised extension of [KadenThomp36/air-quality-card](https://github.com/KadenThomp36/air-quality-card). It preserves the upstream MIT licence and attribution while adding first-class support for NO₂, O₃, SO₂, WAQI-style dominant pollutant text, and HCHO sensors that report in ppm.

![Air Quality Card Preview](images/preview.png)

## Index

- [Features](#features)
- [Installation](#installation)
  - [HACS](#hacs)
  - [Manual Installation](#manual-installation)
- [Configuration](#configuration)
  - [Using the Visual Editor](#using-the-visual-editor)
  - [YAML Configuration](#yaml-configuration)
  - [Basic Indoor Example](#basic-indoor-example)
  - [Indoor + Outdoor Comparison Example](#indoor--outdoor-comparison-example)
  - [Outdoor Station Example](#outdoor-station-example)
  - [Indoor HCHO in ppm Example](#indoor-hcho-in-ppm-example)
  - [Configuration Options](#configuration-options)
  - [Air Quality Index entity](#air-quality-index-entity)
  - [Sensor Order](#sensor-order)
  - [Recommendation Action Button](#recommendation-action-button)
  - [Compact Display Mode](#compact-display-mode)
  - [Compact Chart Height](#compact-chart-height)
  - [Expandable Display Mode](#expandable-display-mode)
  - [Custom Thresholds](#custom-thresholds)
  - [Units and Conversions](#units-and-conversions)
  - [Language](#language)
  - [Outdoor Sensors](#outdoor-sensors)
  - [Outdoor-Only Mode](#outdoor-only-mode)
- [Built-in Recommendations](#built-in-recommendations)
- [Health Thresholds](#health-thresholds)
  - [Live Outdoor Pollutant Bands and Sources](#live-outdoor-pollutant-bands-and-sources)
  - [CO (Carbon Monoxide)](#co-carbon-monoxide)
  - [Radon](#radon)
  - [CO2 (Carbon Dioxide)](#co2-carbon-dioxide)
  - [PM2.5 (Fine Particulate Matter)](#pm25-fine-particulate-matter)
  - [PM10 (Coarse Particulate Matter)](#pm10-coarse-particulate-matter)
  - [PM1 (Ultrafine Particulate Matter)](#pm1-ultrafine-particulate-matter)
  - [PM0.3 (Particle Count)](#pm03-particle-count)
  - [HCHO (Formaldehyde)](#hcho-formaldehyde)
  - [tVOC (Volatile Organic Compounds)](#tvoc-volatile-organic-compounds)
  - [NOx (Nitrogen Oxides)](#nox-nitrogen-oxides)
  - [NO₂, O₃, and SO₂](#no₂-o₃-and-so₂)
  - [Humidity](#humidity)
  - [Atmospheric Pressure](#atmospheric-pressure)
- [Supported Devices](#supported-devices)
- [Development](#development)
- [Contributing](#contributing)
- [License](#license)
- [Credits](#credits)

## Features

- **Real-time monitoring** of CO, Radon, CO2, PM2.5, PM10, PM1, PM0.3, HCHO, tVOC, NOx, NO₂, O₃, SO₂, humidity, temperature, and atmospheric pressure
- **WAQI-friendly outdoor pollutants** — separate NO₂, O₃, and SO₂ metric cards with their own history, thresholds, status badges, and order controls
- **Dominant pollutant row** — a text-only informational chip for entities such as WAQI's `pm25`, `no2`, `o3`, and `so2`
- **CO safety alerts** -- critical red warnings for dangerous carbon monoxide levels
- **Radon advisory banner** -- separate long-term health advisory with EPA/WHO thresholds (supports pCi/L and Bq/m3)
- **Gradient-colored graphs** that change color based on air quality levels
- **Interactive hover/touch** to see historical values at any point
- **Source-documented status bands** — EEA hourly bands for live ambient concentrations, WHO/EPA reference values for recommendations, and explicit AQI handling for WAQI entities
- **Actionable recommendations** like "Open Window" or "Run Air Purifier"
- **Outdoor sensor comparison** - optional dashed line overlay with smart ventilation recommendations
- **Shared indoor/outdoor legend** - one compact footer key explains solid indoor and dashed outdoor lines without adding height to every graph
- **Outdoor-only metric graphs in mixed cards** - configure an outdoor pollutant even when there is no indoor counterpart; it renders as its own dashed graph
- **Compact chart height option** - reduce graph block height on dense dashboards with `compact_charts: true`
- **Tap to expand** - click any graph to open the full Home Assistant history view
- **Visual configuration editor** - no YAML required, with collapsible sections for clean organization

## Installation

### HACS

After adding this fork as a custom HACS repository, install it as a Lovelace plugin and refresh the browser. Do not install the upstream card alongside this fork at the same resource path.

New cards should use `type: custom:air-quality-card-plus`. The older `type: custom:air-quality-card` is still registered as a compatibility alias for existing dashboards, but the explicit Plus type avoids browser custom-element collisions when the upstream card is also installed.

### Manual Installation

1. Download `air-quality-card.js` from this repository's release or main branch
2. Copy it to `/config/www/air-quality-card-plus/air-quality-card.js`
3. Add the resource in Home Assistant:
   - Go to Settings → Dashboards → Resources
   - Add `/local/air-quality-card-plus/air-quality-card.js?v=2.13.1` as a JavaScript Module

## Configuration

### Using the Visual Editor

1. Add a new card to your dashboard
2. Search for "Air Quality Card Plus"
3. Configure the entities using the visual editor
4. Primary sensors (CO₂, PM2.5, Humidity, Temperature) are always visible
5. Expand "Additional Sensors" for Radon, CO, HCHO, tVOC, NOx, NO₂, O₃, SO₂, PM1, PM10, PM4, and PM0.3
6. Expand "Outdoor Sensors" for comparison data. These selectors accept any `sensor` entity so WAQI entities are searchable even when Home Assistant does not assign a matching `device_class`.

### YAML Configuration

Recommended card type:

```yaml
type: custom:air-quality-card-plus
```

Compatibility alias for older dashboards:

```yaml
type: custom:air-quality-card
```

```yaml
type: custom:air-quality-card-plus
name: Office Air Quality
co2_entity: sensor.air_quality_co2
pm25_entity: sensor.air_quality_pm25
pm10_entity: sensor.air_quality_pm10
co_entity: sensor.air_quality_co
radon_entity: sensor.wave_1_day_average
radon_longterm_entity: sensor.wave_longterm_average
humidity_entity: sensor.air_quality_humidity
temperature_entity: sensor.air_quality_temperature
air_quality_entity: sensor.air_quality_index
hours_to_show: 24
temperature_unit: C
outdoor_co2_entity: sensor.outdoor_co2
outdoor_pm25_entity: sensor.outdoor_pm25
```

### Basic Indoor Example

Use this as a starting point for a typical indoor sensor. You can remove any unavailable entity; the card does not require a fixed sensor set.

```yaml
type: custom:air-quality-card-plus
name: Living Room Air Quality
hours_to_show: 24
temperature_unit: C
language: auto
compact_charts: true

co2_entity: sensor.living_room_co2
pm25_entity: sensor.living_room_pm25
hcho_entity: sensor.living_room_formaldehyde
hcho_unit: auto
tvoc_entity: sensor.living_room_tvoc
humidity_entity: sensor.living_room_humidity
temperature_entity: sensor.living_room_temperature
```

### Indoor + Outdoor Comparison Example

Outdoor values render as a dashed line over the matching indoor graph. The recommendation strip becomes outdoor-aware: if outside air is unsuitable for ventilation, it says **Keep Windows Closed** and identifies the strongest outdoor reason without adding another panel to the card.

`dominant_pollutant_entity` is intentionally text-only: it appears as an informational row under the card header and is never sent to the graph/history pipeline.

```yaml
type: custom:air-quality-card-plus
name: Home + Outdoor Air Quality
hours_to_show: 24
temperature_unit: C
compact_charts: true

co2_entity: sensor.living_room_co2
pm25_entity: sensor.living_room_pm25
hcho_entity: sensor.living_room_formaldehyde
hcho_unit: auto
humidity_entity: sensor.living_room_humidity
temperature_entity: sensor.living_room_temperature

air_quality_entity: sensor.local_air_quality_index
dominant_pollutant_entity: sensor.outdoor_dominant_pollutant

outdoor_pm25_entity: sensor.outdoor_pm25
outdoor_pm10_entity: sensor.outdoor_pm10
outdoor_no2_entity: sensor.outdoor_nitrogen_dioxide
outdoor_o3_entity: sensor.outdoor_ozone
outdoor_so2_entity: sensor.outdoor_sulphur_dioxide
outdoor_co_entity: sensor.outdoor_carbon_monoxide
outdoor_humidity_entity: sensor.outdoor_humidity
outdoor_temperature_entity: sensor.outdoor_temperature
outdoor_pressure_entity: sensor.outdoor_pressure
```

### Outdoor Station Example

For a dedicated outdoor card, use the normal metric keys. The card then treats those readings as its primary data and does not show indoor actions such as opening windows or running a purifier.

```yaml
type: custom:air-quality-card-plus
name: Neighbourhood Air Quality
hours_to_show: 24
temperature_unit: C
compact_charts: true

air_quality_entity: sensor.outdoor_air_quality_index
dominant_pollutant_entity: sensor.outdoor_dominant_pollutant

pm25_entity: sensor.outdoor_pm25
pm10_entity: sensor.outdoor_pm10
no2_entity: sensor.outdoor_nitrogen_dioxide
o3_entity: sensor.outdoor_ozone
so2_entity: sensor.outdoor_sulphur_dioxide
co_entity: sensor.outdoor_carbon_monoxide
humidity_entity: sensor.outdoor_humidity
temperature_entity: sensor.outdoor_temperature
pressure_entity: sensor.outdoor_pressure

order:
  - pm25
  - pm10
  - no2
  - o3
  - so2
  - co
  - humidity
  - temperature
  - pressure
```

### Indoor HCHO in ppm Example

```yaml
type: custom:air-quality-card-plus
name: Bedroom Air Quality
hours_to_show: 24
temperature_unit: C
radon_unit: auto
language: auto

co2_entity: sensor.bedroom_co2
pm25_entity: sensor.bedroom_pm25
hcho_entity: sensor.bedroom_formaldehyde
hcho_unit: ppm
tvoc_entity: sensor.bedroom_tvoc

humidity_entity: sensor.bedroom_humidity
temperature_entity: sensor.bedroom_temperature
outdoor_humidity_entity: sensor.outdoor_humidity
outdoor_temperature_entity: sensor.outdoor_temperature

show_min_max: false
tvoc_unit: auto
compact_charts: true
```

### Configuration Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `name` | string | No | "Air Quality" | Card title |
| `co2_entity` | string | No* | - | CO2 sensor entity ID |
| `pm25_entity` | string | No* | - | PM2.5 sensor entity ID |
| `pm1_entity` | string | No* | - | PM1 sensor entity ID |
| `pm10_entity` | string | No* | - | PM10 sensor entity ID |
| `pm03_entity` | string | No* | - | PM0.3 particle count sensor entity ID |
| `no2_entity` | string | No* | - | Nitrogen dioxide (NO₂) sensor entity ID |
| `o3_entity` | string | No* | - | Ozone (O₃) sensor entity ID |
| `so2_entity` | string | No* | - | Sulphur dioxide (SO₂) sensor entity ID |
| `co_entity` | string | No* | - | Carbon monoxide (CO) sensor entity ID |
| `radon_entity` | string | No* | - | Radon sensor entity ID (supports pCi/L and Bq/m3) |
| `radon_longterm_entity` | string | No | - | Radon long-term average sensor (shown as dashed overlay on radon graph) |
| `hcho_entity` | string | No* | - | Formaldehyde (HCHO) sensor entity ID |
| `tvoc_entity` | string | No* | - | Volatile organic compounds (tVOC) sensor entity ID (absolute ppb or Sensirion VOC Index — auto-detected, see `tvoc_unit`) |
| `nox_entity` | string | No* | - | NOx sensor entity ID (absolute ppb or Sensirion NOx Index — auto-detected, see `nox_unit`) |
| `pm4_entity` | string | No* | - | PM4 sensor entity ID |
| `humidity_entity` | string | No* | - | Humidity sensor entity ID |
| `temperature_entity` | string | No* | - | Temperature sensor entity ID |
| `pressure_entity` | string | No* | - | Atmospheric pressure sensor entity ID (e.g. Airthings) |
| `air_quality_entity` | string | No | - | Overall air quality index entity ([passthrough — see below](#air-quality-index-entity)) |
| `dominant_pollutant_entity` | string | No | - | Informational text entity (for example `pm25`, `no2`, `o3`); shown as a friendly label, never plotted |
| `hours_to_show` | number | No | 24 | Hours of history to display (1-168) |
| `temperature_unit` | string | No | "auto" | Temperature unit: "auto" (detect from HA), "F" (Fahrenheit), or "C" (Celsius) |
| `radon_unit` | string | No | "auto" | Radon unit: "auto" (detect from sensor), "pCi/L" (US), or "Bq/m3" (International) |
| `hcho_unit` | string | No | "auto" | HCHO unit: "auto" (detect from entity), "ppb", or "ppm" |
| `tvoc_unit` | string | No | "auto" | tVOC measurement type: "auto" (detect from sensor), "ppb" (absolute), or "index" (Sensirion VOC Index) |
| `nox_unit` | string | No | "auto" | NOx measurement type: "auto" (detect from sensor), "ppb" (absolute), or "index" (Sensirion NOx Index) |
| `show_min_max` | boolean | No | `false` | Overlay the min/max values of the displayed time window directly at the data points on the graph |
| `compact_charts` | boolean | No | `false` | Reduce graph block height/padding while keeping the same chart data, current values, status badges, and interactions |
| `order` | array | No | default | Custom display order for metrics (see [Sensor Order](#sensor-order)) |
| `display` | string | No | "full" | "full" (graphs and details), "compact" (status badge only), or "expandable" (compact, tap to expand to full) |
| `compact_alerts` | boolean | No | `true` | In the compact/collapsed view, show small colored chips naming the sensors currently out of range |
| `auto_expand` | boolean | No | `false` | With `display: expandable`, expand automatically while any sensor is out of range and collapse once readings have stayed normal for 5 minutes. A manual tap takes over until the card is re-created (page reload or dashboard edit) |
| `tap_action` | action | No | - | Standard HA action object (e.g., `{ action: navigate, navigation_path: /air-quality }`). Active in compact mode |
| `hold_action` | action | No | - | Same as `tap_action` but fired after holding for ~500 ms |
| `double_tap_action` | action | No | - | Same as `tap_action` but fired on double-tap |
| `recommendation_action` | action | No | - | Standard HA action surfaced as a one-tap button on the recommendation strip (e.g. toggle a purifier/fan, or run a script). Shown only when there's an actionable recommendation |
| `co_thresholds` | array | No | `[4, 9, 35, 100]` ppm / `[50, 100, 150, 200]` for detected WAQI AQI | Custom CO color/status thresholds (4 ascending numbers defining 5 tiers) |
| `co2_thresholds` | array | No | `[600, 800, 1000, 1500]` | Custom CO₂ color/status thresholds |
| `pm25_thresholds` | array | No | `[5, 15, 50, 90]` µg/m³ / `[50, 100, 150, 200]` for detected WAQI AQI | Custom PM2.5 thresholds |
| `pm10_thresholds` | array | No | `[15, 45, 120, 195]` µg/m³ / `[50, 100, 150, 200]` for detected WAQI AQI | Custom PM10 thresholds |
| `pm1_thresholds` | array | No | `[5, 15, 25, 35]` | Custom PM1 thresholds |
| `pm03_thresholds` | array | No | `[500, 1000, 3000, 5000]` | Custom PM0.3 thresholds |
| `pm4_thresholds` | array | No | `[10, 25, 37.5, 50]` | Custom PM4 thresholds |
| `no2_thresholds` | array | No | `[10, 25, 60, 100]` µg/m³ / `[50, 100, 150, 200]` for detected WAQI AQI | Custom NO₂ thresholds |
| `o3_thresholds` | array | No | `[60, 100, 120, 160]` µg/m³ / `[50, 100, 150, 200]` for detected WAQI AQI | Custom O₃ thresholds |
| `so2_thresholds` | array | No | `[20, 40, 125, 190]` µg/m³ / `[50, 100, 150, 200]` for detected WAQI AQI | Custom SO₂ thresholds |
| `hcho_thresholds` | array | No | `[20, 50, 100, 200]` ppb / `[0.020, 0.050, 0.100, 0.200]` ppm | Custom HCHO thresholds in the selected `hcho_unit` |
| `tvoc_thresholds` | array | No | mode-dependent | Custom tVOC thresholds (units depend on `tvoc_unit`) |
| `nox_thresholds` | array | No | mode-dependent | Custom NOx thresholds (units depend on `nox_unit`; defaults `[20, 53, 100, 360]` ppb / `[5, 20, 150, 300]` index) |
| `radon_thresholds` | array | No | `[48, 100, 148, 300]` | Custom radon thresholds (Bq/m³ — even if you display in pCi/L) |
| `humidity_thresholds` | array | No | `[30, 40, 50, 60]` | Custom humidity thresholds (%) |
| `pressure_thresholds` | array | No | `[990, 1005, 1025, 1040]` | Custom atmospheric pressure thresholds (hPa by default; override for inHg/mmHg) |
| `outdoor_pressure_entity` | string | No | - | Outdoor atmospheric pressure sensor for comparison |
| `temperature_thresholds` | array | No | unit-dependent | Custom temperature thresholds (in the unit your sensor reports) |
| `language` | string | No | "auto" | UI language. "auto" (use Home Assistant's), "en", "es", "fr", "de", or "pt" |
| `outdoor_co2_entity` | string | No | - | Outdoor CO2 sensor for comparison |
| `outdoor_pm25_entity` | string | No | - | Outdoor PM2.5 sensor for comparison |
| `outdoor_pm1_entity` | string | No | - | Outdoor PM1 sensor for comparison |
| `outdoor_pm10_entity` | string | No | - | Outdoor PM10 sensor for comparison |
| `outdoor_pm03_entity` | string | No | - | Outdoor PM0.3 sensor for comparison |
| `outdoor_pm4_entity` | string | No | - | Outdoor PM4 sensor for comparison |
| `outdoor_co_entity` | string | No | - | Outdoor CO sensor for comparison |
| `outdoor_hcho_entity` | string | No | - | Outdoor HCHO sensor for comparison |
| `outdoor_tvoc_entity` | string | No | - | Outdoor tVOC sensor for comparison |
| `outdoor_nox_entity` | string | No | - | Outdoor NOx sensor for comparison |
| `outdoor_no2_entity` | string | No | - | Outdoor NO₂ sensor for comparison |
| `outdoor_o3_entity` | string | No | - | Outdoor O₃ sensor for comparison |
| `outdoor_so2_entity` | string | No | - | Outdoor SO₂ sensor for comparison |
| `outdoor_humidity_entity` | string | No | - | Outdoor humidity sensor for comparison |
| `outdoor_temperature_entity` | string | No | - | Outdoor temperature sensor for comparison |

\* At least one sensor entity is required. Use any combination that fits your setup.

### Air Quality Index entity

The optional `air_quality_entity` is a **passthrough**: whatever value the entity reports is shown directly on the status badge (lowercased and mapped to a color based on standard HA AQI states — `good` / `moderate` / `fair` / `poor` / `very_poor` / `extremely_poor`). The card doesn't interpret it as indoor or outdoor — use whichever entity fits your dashboard.

If you leave it unset, the card computes the status itself from your configured CO / CO₂ / PM2.5 / radon sensors (CO and radon are prioritized as life-safety/health concerns).

### Sensor Order

Customize which sensors come first on the card. In the visual editor, use the multi-select to tick metrics in the order you want them shown. In YAML, provide a list of metric names. Any metric you don't list keeps its default position and stays visible.

Valid metric names: `co`, `radon`, `co2`, `pm25`, `pm10`, `pm1`, `pm03`, `pm4`, `hcho`, `tvoc`, `nox`, `no2`, `o3`, `so2`, `humidity`, `temperature`, `pressure`. `dominant_pollutant_entity` is an informational header row, not a graph metric, so it is not included in `order`.

```yaml
type: custom:air-quality-card-plus
co2_entity: sensor.air_quality_co2
humidity_entity: sensor.air_quality_humidity
temperature_entity: sensor.air_quality_temp
pm10_entity: sensor.air_quality_pm10
pm25_entity: sensor.air_quality_pm25
order:
  - temperature
  - humidity
  - co2
  - pm10
  - pm25
```

### Recommendation Action Button

Surface a one-tap button on the recommendation strip to act on what the card suggests — e.g. turn on an air purifier when it recommends "Run Air Purifier", or run a script. Uses Home Assistant's [standard action](https://www.home-assistant.io/dashboards/actions/) format, so it supports `perform-action`, `toggle`, `navigate`, `url`, `more-info`, etc. The button appears only when there's an actionable recommendation (hidden when air quality is "All Good").

```yaml
type: custom:air-quality-card-plus
co2_entity: sensor.air_quality_co2
pm25_entity: sensor.air_quality_pm25
recommendation_action:
  action: perform-action
  perform_action: homeassistant.toggle
  target:
    entity_id: fan.air_purifier
```

For per-recommendation routing (different action depending on whether it's a purifier vs. ventilation suggestion), point this at a script that branches on your sensor states.

### Compact Display Mode

For overview dashboards where you want a small "go to the air quality page" indicator, use `display: compact`. Renders just the title and the overall status badge, with optional [HA tap actions](https://www.home-assistant.io/dashboards/actions/).

When any sensor reads outside its normal range, small colored **alert chips** appear next to the badge naming the offenders (most severe first — CO and radon always lead — capped at 4 plus a `+N` overflow pill) — so a yellow badge tells you *what* is off without expanding. Set `compact_alerts: false` to turn the chips off. Atmospheric pressure never produces a chip or triggers auto-expand: it's informational, not an air-quality hazard.

```yaml
type: custom:air-quality-card-plus
name: Air Quality
co2_entity: sensor.air_quality_co2
pm25_entity: sensor.air_quality_pm25
display: compact
tap_action:
  action: navigate
  navigation_path: /lovelace/air-quality
```

Compact mode:
- Skips the history fetch (faster initial load)
- Status badge updates in real-time from current sensor values
- All three standard HA actions are supported: `tap_action`, `hold_action`, `double_tap_action`

### Compact Chart Height

If the full card feels too tall but you still want every graph visible, set `compact_charts: true`. This keeps the normal full-card layout and interactions, but reduces chart padding while giving the plotted line **34 px** of height. The redundant X-axis labels are removed from every graph; hover or touch still shows the exact historical time in the tooltip. The compact graph uses the reclaimed axis space, so it is more legible without making the card taller.

```yaml
type: custom:air-quality-card-plus
name: Bedroom Air Quality
compact_charts: true
co2_entity: sensor.bedroom_air_sensor_carbon_dioxide
pm25_entity: sensor.bedroom_air_sensor_pm2_5
hcho_entity: sensor.bedroom_air_sensor_formaldehyde_concentration
```

### Expandable Display Mode

`display: expandable` starts as a compact summary and expands to the full card (graphs and details) when tapped — best of both worlds for space-constrained dashboards. A chevron indicates the toggle; tap the header again to collapse. History is fetched lazily the first time you expand.

```yaml
type: custom:air-quality-card-plus
name: Air Quality
co2_entity: sensor.air_quality_co2
pm25_entity: sensor.air_quality_pm25
display: expandable
```

In expandable mode the tap gesture is reserved for expand/collapse, so `tap_action` is ignored.

Add `auto_expand: true` to let the card manage itself: it expands automatically while any sensor reads out of range and collapses once readings have stayed normal for 5 minutes (the delay prevents flapping when a sensor hovers at a threshold). A manual tap takes over (the card stops auto-toggling until it is re-created, e.g. a page reload or dashboard edit), so the automation never fights you.

```yaml
type: custom:air-quality-card-plus
co2_entity: sensor.air_quality_co2
pm25_entity: sensor.air_quality_pm25
display: expandable
auto_expand: true
```

### Custom Thresholds

The defaults use a mixture of EEA live-air bands, WHO reference values, and comfort guidance; see [Health Thresholds](#health-thresholds). You can override any threshold set. Provide an array of **4 ascending numbers** — these become the 5-tier boundaries (see the metric tables for labels).

```yaml
type: custom:air-quality-card-plus
temperature_entity: sensor.living_room_temp
temperature_unit: C
# In tropical climates, 26-29 °C is comfortable AC territory
temperature_thresholds: [22, 25, 28, 31]
# Stricter PM2.5 dashboard bands, in this sensor's reported unit
pm25_thresholds: [3, 8, 15, 25]
```

Notes:
- Custom thresholds are in the displayed/source unit. For HCHO, they are in the selected `hcho_unit`; the card converts ppm to internal ppb values before calculating status colors. For radon, the thresholds are always in **Bq/m³** — the card converts your sensor value before comparison.
- A detected WAQI entity uses an **individual AQI** by default. If you override its thresholds, enter AQI boundaries, not concentrations.
- Invalid thresholds (wrong length, non-numeric, etc.) silently fall back to the defaults. No errors thrown.
- Colors are not customizable — only the boundaries between them.

### Units and Conversions

The card always displays the unit supplied by the selected Home Assistant entity. Built-in default bands for PM2.5, PM10, NO₂, O₃, and SO₂ are defined in **µg/m³** and are converted before status/colour calculation when Home Assistant reports a supported alternative:

- `mg/m³` is converted to `µg/m³`.
- For NO₂, O₃, and SO₂, `ppb` is converted at 25 °C and 1013 mbar using 1.88, 1.96, and 2.62 µg/m³ per ppb respectively.
- A mixed indoor/outdoor graph converts supported concentration pairs to the unit of its primary line, so its line, axis, tooltip, current-value suffix, and colour tiers stay aligned.
- If the two sources do not have a safely compatible concentration unit, they are not drawn on the same graph. Their current values still appear with their own units.

#### WAQI entities are AQI, not concentrations

The official Home Assistant WAQI integration forwards WAQI's `iaqi.*.v` field. WAQI describes these as **individual AQI** values, and the HA integration does not attach a physical unit to the pollutant entities. The card detects the WAQI attribution automatically and displays `AQI` rather than inventing `ppm` or `µg/m³`.

For example, a WAQI CO value of `0.1` is shown as `0 AQI` (rounded for display), **not** `0.1 ppm`; it cannot be converted to a CO concentration. A WAQI PM2.5 value of `38` is `PM2.5 AQI 38`, not `38 µg/m³`. Detected WAQI values use common AQI bands `[50, 100, 150, 200]`, and they are not overlaid on an indoor physical-concentration graph. This prevents a visually tidy but scientifically false comparison. WAQI's API calls these “individual AQI” values; the HA source code forwards them directly. [WAQI API](https://aqicn.org/api/waqi.info) and [Home Assistant WAQI source](https://github.com/home-assistant/core/blob/dev/homeassistant/components/waqi/sensor.py).

### Language

The card auto-detects your Home Assistant frontend language and translates the status badge, recommendations, recommendation subtitles, radon advisory titles, and editor labels. Translations included so far: **English, Spanish, French, German, Portuguese** (Spanish/French/German contributed by [@b0rv3g4r4](https://github.com/b0rv3g4r4) on PR #11, Portuguese by [@mzspicoli](https://github.com/mzspicoli) on PR #33).

If auto-detection picks the wrong language, force one explicitly with `language: es` (or `en` / `fr` / `de` / `pt`).

To contribute a new language: open a PR adding a block to the `TRANSLATIONS` const in `air-quality-card.js`. Copy the `en:` block, rename the key (e.g. `it:` for Italian), and translate the values — keep the structure identical. English is the fallback for any missing key.

### Outdoor Sensors

Configure outdoor sensor entities to see a **dashed comparison line** on each graph showing outdoor conditions alongside indoor readings. When outdoor sensors are configured:

- A subtle dashed line appears on the corresponding graph
- Hovering shows both indoor and outdoor values
- Current outdoor values appear next to indoor readings
- One compact card-footer legend explains the line styles: solid = indoor, dashed = outdoor
- **Outdoor-aware recommendations** avoid suggesting ventilation when outdoor air is unsuitable (e.g., "Keep Windows Closed" instead of "Open Window") and identify the strongest outdoor reason in the existing recommendation strip

If you configure an outdoor entity without the matching indoor entity on the same mixed indoor card, it still renders as its own graph with a dashed line. The shared footer legend still identifies it as outdoor, without spending a line of height in that graph.

Use `outdoor_no2_entity` for a direct nitrogen dioxide measurement. `outdoor_nox_entity` remains available for aggregate NOx or Sensirion NOx Index sensors, but it displays as NOx rather than NO₂.

For any compatible outdoor concentration source, use the `outdoor_*` keys on an indoor card:

```yaml
type: custom:air-quality-card-plus
name: Bedroom + Outdoor Air Quality
compact_charts: true

co2_entity: sensor.bedroom_co2
pm25_entity: sensor.bedroom_pm25
hcho_entity: sensor.bedroom_formaldehyde
tvoc_entity: sensor.bedroom_tvoc
humidity_entity: sensor.bedroom_humidity
temperature_entity: sensor.bedroom_temperature

outdoor_pm25_entity: sensor.outdoor_pm25
outdoor_pm10_entity: sensor.outdoor_pm10
outdoor_no2_entity: sensor.outdoor_nitrogen_dioxide
outdoor_o3_entity: sensor.outdoor_ozone
outdoor_so2_entity: sensor.outdoor_sulphur_dioxide
outdoor_co_entity: sensor.outdoor_carbon_monoxide
outdoor_humidity_entity: sensor.outdoor_humidity
outdoor_temperature_entity: sensor.outdoor_temperature
outdoor_pressure_entity: sensor.outdoor_pressure
```

For a WAQI source, use the same keys, but remember its pollutant values are individual AQI values. On a mixed card, the card shows their current `AQI` values beside your indoor concentration, but deliberately does not draw the incompatible lines over one another. A dedicated outdoor card works especially well for a station-wide WAQI view.

### Outdoor-Only Mode

If you're using the card to monitor **only outdoor air quality** (e.g., a weather station or DIY ESPHome ambient sensor), you can configure just the `outdoor_*_entity` options. The card will:

- Render outdoor entities as primary graph lines
- Compute the status badge from the outdoor values using the appropriate concentration or detected WAQI AQI bands
- **Hide the recommendation strip** — actions like "Open Window" or "Run Air Purifier" don't apply to ambient air

```yaml
type: custom:air-quality-card-plus
name: Outdoor Air Quality
outdoor_pm25_entity: sensor.outdoor_pm25
outdoor_pm10_entity: sensor.outdoor_pm10
outdoor_temperature_entity: sensor.outdoor_temperature
```

## Built-in Recommendations

The card automatically generates actionable recommendations based on your sensor readings -- no template sensors needed. It evaluates CO, CO2, PM2.5, PM10, HCHO, tVOC, and humidity levels. When outdoor sensors are configured, it avoids suggesting ventilation when outdoor air is unsuitable.

If an outdoor concentration crosses a conservative short-term ventilation guardrail, or the outside temperature would make an already-hot/cold room less comfortable, the existing recommendation strip says **Keep Windows Closed** and names the reason — for example, `Outdoor PM2.5: 38.0 µg/m³ (Moderate)` or `Outdoor 29 °C is warmer than indoors`. It does not add another card section.

| Outdoor signal | Concentration guardrail | Basis |
|---|---:|---|
| PM2.5 | 15 µg/m³ | WHO 24-hour AQG |
| PM10 | 45 µg/m³ | WHO 24-hour AQG |
| NO₂ | 25 µg/m³ | WHO 24-hour AQG |
| O₃ | 100 µg/m³ | WHO 8-hour AQG |
| SO₂ | 40 µg/m³ | WHO 24-hour AQG |
| CO | 9 ppm | EPA 8-hour standard |
| Detected WAQI pollutant | AQI 50 | Start of the moderate AQI band |

These are real-time decision aids, not compliance determinations: a live point is not automatically the same as an 8- or 24-hour regulatory average. Indoor CO safety alerts always take priority over a window recommendation.

**CO safety alerts** from a physical ppm CO sensor are always shown regardless of outdoor conditions -- carbon monoxide is a life-safety concern. If CO exceeds dangerous levels, the card shows a critical red warning with instructions to leave the area. A WAQI CO state is an AQI, not an indoor CO concentration, so it never triggers this life-safety alarm.

**Radon advisory banner** appears as a separate element below the main recommendation when radon levels are elevated. Unlike other pollutants, radon changes over days/weeks and requires professional mitigation (not "open a window"), so it uses its own advisory system instead of the main recommendation waterfall. The advisory shows at three levels: informational (approaching action level), warning (above EPA action level of 4.0 pCi/L / 148 Bq/m3), and danger (significantly elevated, mitigation needed).

## Health Thresholds

### Live Outdoor Pollutant Bands and Sources

For physical-concentration entities, PM2.5, PM10, NO₂, O₃, and SO₂ use the [European Air Quality Index](https://airindex.eea.europa.eu/AQI/index.html)'s **hourly** bands. The EEA has six categories; this five-colour card combines its final “very poor” and “extremely poor” categories into **Very Poor**. The table below gives the four card boundaries; the ranges are applied as `< boundary`, so the next tier begins at the listed value.

| Pollutant | Card concentration boundaries | Card labels | WHO 2021 reference values and averaging time | Source |
|---|---:|---|---|---|
| PM2.5 | `[5, 15, 50, 90]` µg/m³ | Good / Fair / Moderate / Poor / Very Poor | 5 annual; 15 24-hour | [EEA AQI](https://airindex.eea.europa.eu/AQI/index.html), [WHO AQG](https://www.who.int/news-room/questions-and-answers/item/who-global-air-quality-guidelines) |
| PM10 | `[15, 45, 120, 195]` µg/m³ | Good / Fair / Moderate / Poor / Very Poor | 15 annual; 45 24-hour | [EEA AQI](https://airindex.eea.europa.eu/AQI/index.html), [WHO AQG](https://www.who.int/news-room/questions-and-answers/item/who-global-air-quality-guidelines) |
| NO₂ | `[10, 25, 60, 100]` µg/m³ | Good / Fair / Moderate / Poor / Very Poor | 10 annual; 25 24-hour | [EEA AQI](https://airindex.eea.europa.eu/AQI/index.html), [WHO AQG](https://www.who.int/news-room/questions-and-answers/item/who-global-air-quality-guidelines) |
| O₃ | `[60, 100, 120, 160]` µg/m³ | Good / Fair / Moderate / Poor / Very Poor | 60 peak-season daily max 8-hour; 100 8-hour | [EEA AQI](https://airindex.eea.europa.eu/AQI/index.html), [WHO AQG](https://www.who.int/news-room/questions-and-answers/item/who-global-air-quality-guidelines) |
| SO₂ | `[20, 40, 125, 190]` µg/m³ | Good / Fair / Moderate / Poor / Very Poor | 40 24-hour | [EEA AQI](https://airindex.eea.europa.eu/AQI/index.html), [WHO AQG](https://www.who.int/news-room/questions-and-answers/item/who-global-air-quality-guidelines) |

The EEA bands are useful for a **live dashboard colour/status**. WHO values are health-protective exposure guidelines with explicit averaging windows, not an instruction to treat one instantaneous point as a legal exceedance. The recommendation strip deliberately uses the WHO short-term values as conservative ventilation guardrails.

Detected WAQI pollutant entities are a different data type: they use the common individual-AQI boundaries `[50, 100, 150, 200]` and display `AQI`. WAQI calls its `iaqi` values individual AQI; it does not provide the physical unit needed to apply the concentration table above. See [Units and Conversions](#units-and-conversions).

### CO (Carbon Monoxide)
For a physical CO entity reported in ppm:
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Safe | < 4 ppm | Green | Normal background levels |
| Low | 4-9 ppm | Light Green | Acceptable for short exposure |
| Moderate | 9-35 ppm | Yellow | Improve ventilation |
| High | 35-100 ppm | Orange | Ventilate immediately |
| Dangerous | > 100 ppm | Red | Leave area immediately |

The EPA primary standards are 9 ppm over 8 hours and 35 ppm over 1 hour. The card's five display bands are operational guidance around those values, not a substitute for a certified CO alarm or emergency advice. A detected WAQI CO entity is an individual AQI and uses the separate AQI bands instead of this table. [EPA CO standards](https://www.epa.gov/co-pollution/timeline-carbon-monoxide-co-national-ambient-air-quality-standards-naaqs).

### Radon
Based on EPA and WHO guidelines:
| Level | Range (pCi/L) | Range (Bq/m3) | Color | Meaning |
|-------|---------------|----------------|-------|---------|
| Excellent | < 1.3 | < 48 | Green | Low risk |
| Good | 1.3-2.7 | 48-100 | Light Green | Below WHO reference level |
| Elevated | 2.7-4.0 | 100-148 | Yellow | Approaching EPA action level |
| High | 4.0-8.0 | 148-300 | Orange | Above EPA action level, consider mitigation |
| Dangerous | > 8.0 | > 300 | Red | Professional mitigation needed |

### CO2 (Carbon Dioxide)
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Excellent | < 600 ppm | Green | Fresh outdoor air levels |
| Good | 600-800 ppm | Light Green | Well-ventilated space |
| Moderate | 800-1000 ppm | Yellow | Acceptable, consider ventilation |
| Elevated | 1000-1500 ppm | Orange | May affect concentration |
| Poor | > 1500 ppm | Red | Ventilation needed |

### PM2.5 (Fine Particulate Matter)
Physical-concentration entities use the EEA hourly display bands; WHO annual/24-hour reference values are in [Live Outdoor Pollutant Bands and Sources](#live-outdoor-pollutant-bands-and-sources).
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Good | < 5 µg/m³ | Green | EEA good |
| Fair | 5-15 µg/m³ | Light Green | EEA fair |
| Moderate | 15-50 µg/m³ | Yellow | EEA moderate |
| Poor | 50-90 µg/m³ | Orange | EEA poor |
| Very Poor | ≥ 90 µg/m³ | Red | EEA very/extremely poor combined |

### PM10 (Coarse Particulate Matter)
Physical-concentration entities use the EEA hourly display bands; WHO annual/24-hour reference values are in [Live Outdoor Pollutant Bands and Sources](#live-outdoor-pollutant-bands-and-sources).
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Good | < 15 µg/m³ | Green | EEA good |
| Fair | 15-45 µg/m³ | Light Green | EEA fair |
| Moderate | 45-120 µg/m³ | Yellow | EEA moderate |
| Poor | 120-195 µg/m³ | Orange | EEA poor |
| Very Poor | ≥ 195 µg/m³ | Red | EEA very/extremely poor combined |

### PM1 (Ultrafine Particulate Matter)
There is no single WHO/EPA ambient guideline specifically for PM1. These are indicative dashboard bands; set `pm1_thresholds` to suit your sensor/use case.
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Excellent | < 5 µg/m³ | Green | Clean air |
| Good | 5-15 µg/m³ | Light Green | Acceptable |
| Moderate | 15-25 µg/m³ | Yellow | Slightly elevated |
| Elevated | 25-35 µg/m³ | Orange | Consider air purifier |
| Poor | > 35 µg/m³ | Red | Air purifier recommended |

### PM0.3 (Particle Count)
Particle count is not directly comparable to PM mass concentrations and has no single WHO/EPA ambient guideline. These are indicative dashboard bands; set `pm03_thresholds` to suit your sensor/use case.
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Clean | < 500 p/0.1L | Green | Very clean air |
| Good | 500-1000 p/0.1L | Light Green | Normal levels |
| Moderate | 1000-3000 p/0.1L | Yellow | Slightly elevated |
| Elevated | 3000-5000 p/0.1L | Orange | Consider air purifier |
| Poor | > 5000 p/0.1L | Red | Air purifier recommended |

### HCHO (Formaldehyde)

Set `hcho_unit: auto`, `ppb`, or `ppm`. In `auto` mode the card reads the entity's `unit_of_measurement`; sensors without a recognised unit retain the backwards-compatible `ppb` behavior. HCHO status colors and thresholds are always evaluated in ppb internally, but ppm readings are displayed and graphed in ppm with two decimal places (for example, `0.04 ppm`). The default bands are `[20, 50, 100, 200]` ppb, equivalent to `[0.020, 0.050, 0.100, 0.200]` ppm.

WHO's indoor-air guideline is 0.1 mg/m³ as a 30-minute average (roughly 80 ppb at standard reference conditions). The card's surrounding five bands are practical display bands, not a direct replacement for that time-weighted guideline. [WHO pollutant guidance](https://www.who.int/teams/environment-climate-change-and-health/air-quality-and-health/health-impacts/types-of-pollutants).

| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Excellent | < 20 ppb | Green | Safe levels |
| Good | 20-50 ppb | Light Green | Acceptable |
| Moderate | 50-100 ppb | Yellow | Consider ventilation |
| Elevated | 100-200 ppb | Orange | Ventilation needed |
| Poor | > 200 ppb | Red | Take action |

### tVOC (Volatile Organic Compounds)
There is no universal health threshold for a total VOC measurement: it depends on the individual compounds. The following bands are indicative indoor-air dashboard guidance; use a compound-specific sensor/reference where a compliance or health decision is required.
The card auto-detects whether your sensor reports absolute concentration (ppb) or the unitless Sensirion VOC Index (0-500, centered at 100); force a mode with `tvoc_unit` if needed.

Absolute (ppb):
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Excellent | < 100 ppb | Green | Clean air |
| Good | 100-300 ppb | Light Green | Acceptable |
| Moderate | 300-500 ppb | Yellow | Consider ventilation |
| Elevated | 500-1000 ppb | Orange | Ventilation needed |
| Poor | > 1000 ppb | Red | Take action |

VOC Index (Sensirion):
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Excellent | < 100 | Green | Better than your home's average |
| Good | 100-150 | Light Green | Around average |
| Moderate | 150-250 | Yellow | Consider ventilation |
| Elevated | 250-400 | Orange | Ventilation needed |
| Poor | > 400 | Red | Take action |

### NOx (Nitrogen Oxides)
Like tVOC, NOx comes in two flavors and the card auto-detects which one your sensor reports (force with `nox_unit`). Sensirion SGP41-based sensors (AirGradient ONE / Open Air, ESPHome `sgp4x`) report the unitless **NOx Index** — note its clean-air baseline is **1**, not 100 like the VOC Index.

`nox_entity` remains for aggregate NOx concentration or the Sensirion **NOx Index**. Use `no2_entity` (or `outdoor_no2_entity` on mixed indoor/outdoor cards) for a direct nitrogen dioxide (NO₂) measurement; it has a separate graph, state, status badge, thresholds, and history, and never reuses the NOx index behavior.

NOx Index (Sensirion) — bands follow Sensirion's integration guidance and AirGradient's dashboard:
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Excellent | < 5 | Green | Clean-air baseline (1) |
| Good | 5-20 | Light Green | Minor activity |
| Moderate | 20-150 | Yellow | NOx event — Sensirion's "trigger an air purifier" level |
| Elevated | 150-300 | Orange | Significant NOx event — ventilate |
| Poor | > 300 | Red | Major NOx event |

Absolute (ppb) — anchored to WHO 2021 / EPA NO₂ standards (applied to NOx conservatively). If your sensor reports µg/m³ instead of ppb, the card displays that unit but the default thresholds still assume ppb — set `nox_thresholds` in your sensor's unit (for NO₂, 1 ppb ≈ 1.88 µg/m³):
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Excellent | < 20 ppb | Green | ~WHO interim target 1 (40 µg/m³) |
| Good | 20-53 ppb | Light Green | Under the EPA annual NAAQS (53 ppb) |
| Moderate | 53-100 ppb | Yellow | Under the EPA 1-hour NAAQS (100 ppb) |
| Elevated | 100-360 ppb | Orange | EPA AQI Unhealthy-for-Sensitive-Groups range |
| Poor | > 360 ppb | Red | EPA AQI Unhealthy and above |

### NO₂, O₃, and SO₂

These standalone pollutant metrics work with physical-concentration sensors and WAQI. For physical concentration, their default status bands are the EEA hourly bands shown in [Live Outdoor Pollutant Bands and Sources](#live-outdoor-pollutant-bands-and-sources). The card keeps the entity's own unit label and automatically normalises supported `µg/m³`, `mg/m³`, and gas `ppb` values for its default colour/status calculation.

The official Home Assistant WAQI integration is handled separately: its pollutant states are individual **AQI** values rather than concentrations, so they display `AQI` and use `[50, 100, 150, 200]` AQI boundaries. They must not be read as µg/m³.

For outdoor overlays, use `outdoor_no2_entity`, `outdoor_o3_entity`, and `outdoor_so2_entity`. They support the same history loading, tooltips, ordering, status colours, and outdoor-only mixed-card behaviour as the older outdoor metrics.

| Metric | Default thresholds | Configuration key |
|--------|--------------------|-------------------|
| NO₂ | `[10, 25, 60, 100]` µg/m³ | `no2_thresholds` |
| O₃ | `[60, 100, 120, 160]` µg/m³ | `o3_thresholds` |
| SO₂ | `[20, 40, 125, 190]` µg/m³ | `so2_thresholds` |

### Humidity
| Level | Range | Color | Meaning |
|-------|-------|-------|---------|
| Too Dry | < 30% | Orange | Use humidifier |
| Dry | 30-40% | Light Green | Acceptable |
| Comfortable | 40-50% | Green | Ideal range |
| Humid | 50-60% | Light Green | Acceptable |
| Too Humid | > 60% | Orange | Improve ventilation |

### Atmospheric Pressure
Informational, not a health hazard — a wide green band keeps typical weather calm. Thresholds assume **hPa / mbar** (what Airthings and most HA sensors report); override `pressure_thresholds` for other units.
| Level | Range (hPa) | Color | Meaning |
|-------|-------------|-------|---------|
| Low | < 990 | Orange | Stormy / rapidly falling |
| Slightly Low | 990-1005 | Light Green | Below average |
| Normal | 1005-1025 | Green | Typical sea-level range |
| Slightly High | 1025-1040 | Light Green | Above average |
| High | > 1040 | Orange | Unusually high |

## Supported Devices

This card works with any sensor that provides entities for CO, Radon, CO2, PM2.5, PM10, PM4, PM1, PM0.3, HCHO, tVOC, NOx, NO₂, O₃, SO₂, humidity, temperature, atmospheric pressure, or dominant-pollutant text. Use any combination -- even a single sensor works. Tested with:

- IKEA VINDSTYRKA / ALPSTUGA (via Matter)
- Aqara TVOC Air Quality Monitor
- Xiaomi Air Quality Monitor
- SenseAir S8
- AirGradient ONE / Open Air
- PurpleAir sensors
- Airthings Wave / Wave Plus (radon, CO2, tVOC, humidity, temperature)
- Any ESPHome-based air quality sensor

## Development

```bash
# Clone your fork
git clone https://github.com/YOUR_GITHUB_USERNAME/ha-air-quality-card-plus.git

# The card is vanilla JavaScript with no build step required
# Simply edit air-quality-card.js and test in Home Assistant

# Run tests
node test.js
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see [LICENSE](LICENSE) for details.

## Credits

- Forked from [KadenThomp36/air-quality-card](https://github.com/KadenThomp36/air-quality-card); original work and MIT licence preserved.
- Live ambient pollutant bands based on the [European Air Quality Index](https://airindex.eea.europa.eu/AQI/index.html).
- Health reference values based on the [WHO Global Air Quality Guidelines](https://www.who.int/news-room/questions-and-answers/item/who-global-air-quality-guidelines) and [WHO pollutant guidance](https://www.who.int/teams/environment-climate-change-and-health/air-quality-and-health/health-impacts/types-of-pollutants).
- NO₂/O₃/SO₂ ppb-to-µg/m³ factors from [DEFRA's conversion factors](https://uk-air.defra.gov.uk/assets/documents/reports/cat06/0502160851_Conversion_Factors_Between_ppb_and.pdf).
- WAQI handling verified against the [WAQI API](https://aqicn.org/api/waqi.info) and [Home Assistant WAQI integration source](https://github.com/home-assistant/core/blob/dev/homeassistant/components/waqi/sensor.py).
- CO2 recommendations based on [ASHRAE Standard 62.1](https://www.ashrae.org/technical-resources/bookstore/standards-62-1-62-2)
- CO thresholds based on [EPA carbon monoxide standards](https://www.epa.gov/co-pollution/timeline-carbon-monoxide-co-national-ambient-air-quality-standards-naaqs).
