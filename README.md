# TrailtimeUDv2 — TrailTime update & firmware release feed

Public release feed for the **TrailTime V2** race-timing system. Nothing here is
edited by hand except this README — everything else is published by tooling.

## Desktop app updates

The RaceTimer desktop app checks **GitHub Releases** on this repo for updates
(Settings ► check for updates, and at startup):

- Tag each release `vX.Y.Z` (matching the app's assembly version).
- Attach the standalone `TrailTime.exe` as a release asset.
- The app reads `/releases/latest`, compares versions, and offers one-click
  Update & Restart.

## Firmware releases (`firmware/`)

The Device Manager inside the desktop app reads
[`firmware/manifest.json`](firmware/manifest.json) and keeps a local cache in
`%LocalAppData%\RaceTimer\FirmwareCache`. Before every **Flash Only**, it does a
quick version check against this manifest: outdated or missing → download first;
repo unreachable (race venue, no internet) → flash the last-downloaded copy.

| Target key          | Device                                          | Binary |
|---------------------|-------------------------------------------------|--------|
| `heltec-v3`         | TimingPod V2 (RFID + Lidar pods)                | `firmware/heltec-v3/firmware.factory.bin` |
| `timingpod-v2demo`  | Demo pods (Benewake TFmini-S IIC LiDAR)         | `firmware/timingpod-v2demo/firmware.factory.bin` |
| `heltec-v3-gateway` | RaceVillage gateway                             | `firmware/heltec-v3-gateway/firmware.factory.bin` |
| `beacon-esp32c3`    | TrailTime beacon (source-only, built per-bib)   | — version reference only |

The `firmware.factory.bin` files are complete factory images flashed at `0x0`
with esptool. The beacon has no binary here because its bib number is compiled
in per unit; the manifest version lets the Device Manager warn when a local
beacon checkout is behind the fleet reference.

## Publishing a new firmware release

From the (private) `TrailTimeV2` repo on a dev machine:

```powershell
.\tools\publish_firmware.ps1
```

That script builds all PlatformIO targets, regenerates `firmware/manifest.json`
(version, SHA-256, size), commits and pushes to this repo. Bump
`TRAILTIME_FW_VERSION` / `BEACON_FW_VERSION` in the firmware source first —
the manifest version is read from the source tree.
