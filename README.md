# DBDTracker

Dead by Daylight survivor stat tracker, optionally with Intiface/buttplug.io
actuator output. Modeled architecturally on [OverStim](https://github.com/cryo-es/OverStim).

**Key feature: drop a template, edit one YAML block, run.** No coordinate
calibration. No code editing for new events.

## Quickstart

```
pip install -r requirements.txt
```

Then read **[WORKFLOW.md](WORKFLOW.md)** for the template-creation workflow.

## File map

| File                 | Purpose                                                              |
| -------------------- | -------------------------------------------------------------------- |
| `dbd_tracker.py`     | Main entry: asyncio, GUI, buttplug client, event routing             |
| `debug_overlay.py`   | Standalone calibration tool — live preview of template matches       |
| `dbdcv.py`           | dxcam capture + OpenCV template matching with region hints           |
| `dbdstate.py`        | Registry-driven per-frame state tracker                              |
| `stats_manager.py`   | Per-match counter + timer accumulator                                |
| `csv_logger.py`      | Dynamic-schema CSV writer (auto-adjusts columns)                     |
| `vibe_manager.py`    | Game-agnostic Vibe / VibeManager port from OverStim                  |
| `event_registry.py`  | Loads `events.yaml` + auto-discovers template files                  |
| `regions.py`         | Named region bounding boxes at 1920×1080                             |
| `events.yaml`        | **Declarative event config — this is where you add/remove events**   |
| `config.ini`         | Runtime config (capture rate, Intiface, safety caps)                 |
| `data/templates/`    | Drop `t_<name>.png` here                                             |

## What changed from previous versions

This is the third revision. The first version hardcoded coordinates and
per-event detection. The second wired in the full OverStim framework (asyncio +
buttplug + GUI). This third revision keeps everything from the second and:

- **Replaces the hardcoded `coords` dict with auto-discovery from `data/templates/`**
- **Replaces hardcoded vibe wiring with declarative `events.yaml`**
- **Adds `debug_overlay.py` for live calibration without running the full tracker**
- **CSV columns expand automatically when you add new YAML entries**

## Supported devices

Routed through [Intiface Central](https://intiface.com/central/), which is the
GUI for buttplug.io. The buttplug.io library supports 750+ devices including
Lovense, Kiiroo, The Handy, WeVibe, Satisfyer, OSR-2/SR-6, and many more.
Connectivity types include Bluetooth, USB, HID, Serial, and Network-controlled
devices.

The full current list lives at <https://iostindex.com/?filter0Availability=Available,DIY&filter1Connection=Digital&filter2Features=OutputsVibrators>
(iostindex.com is the canonical compatibility database).

Set `USING_INTIFACE = no` in `config.ini` to disable actuator output entirely
(CSV stat tracking still works).

## Safety

- Emergency stop hotkey: `Ctrl+Shift+F10` (configurable in `config.ini`)
- `MAX_VIBE_INTENSITY` hard-caps all output regardless of trigger settings
- `BLEEDING_OUT_EVENT = 1` halts all output when in the dying state
