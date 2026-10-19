# CLAUDE.md

## What This Is

A Home Assistant custom integration for FireBoard wireless thermometers. It polls the FireBoard Cloud REST API and exposes temperature sensors, alert threshold sensors, connectivity, and battery status as HA entities.

The running instance lives at `/Volumes/config/custom_components/fireboard/` on a Raspberry Pi (mounted over the network). HACS installs it from GitHub releases. See "Releasing" and "Testing on the running HA" below.

## Tests

Tests require 80% coverage, set in `pyproject.toml`.

## Architecture

### Entity registration pattern

`sensor.py` uses a coordinator listener instead of a one-shot setup, so new channels and alerts discovered on subsequent polls get entities created without a reload.

### Entity naming

Entity IDs and friendly names are **fully dynamic** — derived from the device `title` and `channel_label` fields returned by the API. Nothing is hardcoded. Renaming a grill or probe in the FireBoard app updates the entity name in HA within one poll cycle.

Pattern: `sensor.<grill_name>_<channel_label>` (e.g. `sensor.smoker_brisket`)

Alert sensors only exist for channels that have an active alert configured in the FireBoard app. They appear/disappear dynamically without a reload.

### API notes

- Auth endpoint is at `/api/rest-auth/login/` (not under `/v1/`)
- Device list with temperatures: `GET /api/v1/devices.json`
- `current_temp` is only present on channels with an active probe reading
- Rate limit: 200 calls/hour. Default 60s polling = ~60 calls/hour.
- On 401: re-authenticate and retry once. On 429: raise `UpdateFailed`.

### Tested hardware

Only tested with the **FireBoard Pellet Drive (model: FBXPD)**. Other models use the same API and should work but are unverified. See `docs/sample_api_response_FBXPD.json` for the full API response shape.

## Releasing

HACS manages the running instance. HACS shows an update in HA only when a GitHub release has a newer version.

1. Change `version` in `custom_components/fireboard/manifest.json` and `pyproject.toml`, and the two version lines in `STATUS.md`. Add a Changelog entry in `README.md`.
2. Merge to `main`.
3. Create the release: `gh release create vX.Y.Z --generate-notes`. The tag must match the manifest version.
4. In HA: Settings → Updates → FireBoard → Update, then restart.

## Testing on the running HA

The HA config share is `smb://homeassistant.unibrain.org/config`. Mount it in Finder (⌘K). It mounts at `/Volumes/config`.

```bash
cp custom_components/fireboard/<file>.py /Volumes/config/custom_components/fireboard/

# Then in HA UI: Settings → Devices & Services → FireBoard → Reload
# (Full restart only needed when adding new entity classes or changing manifest.json)
```

A manual copy makes the files differ from the version that HACS reports. After a test, install the release through HACS again.
