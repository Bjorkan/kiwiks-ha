# Home Assistant App: Kiwiks

Serve offline ZIM files (Wikipedia, etc.) with
[kiwix-serve](https://github.com/kiwix/kiwix-tools). The ZIM files stay
on your NAS. The NAS is mounted **once in Home Assistant itself**, and
this app reads the files through that mount.

No ingress: the web UI is exposed directly on port `8080`.

## How it works

1. You add your NAS in Home Assistant: **Settings → System → Storage →
   Add network storage** (usage **Media** or **Share**), e.g. named
   `kiwiks`. Home Assistant mounts it at `/media/kiwiks` (or
   `/share/kiwiks` for usage Share).
2. You copy `*.zim` files into that storage (from any machine that can
   reach the NAS).
3. This app scans the configured directory at startup and serves every
   `*.zim` it finds via `kiwix-serve` on port `8080`.

## Installation

1. Mount the NAS in Home Assistant (**Settings → System → Storage → Add
   network storage**). Note the name you give it and whether you chose
   **Media** or **Share** usage.
2. Put your ZIM files there, e.g.:
   - Media usage named `kiwiks` → `/media/kiwiks/*.zim`
   - Share usage named `kiwiks` → `/share/kiwiks/*.zim`
3. Add this repository (**Settings → Apps → ⋯ → Repositories**),
   install **Kiwiks**.
4. In the app **Configuration** tab, set `zim_path` to the mount path
   (default `/media/kiwiks`), save, and **Start** the app.
5. Open the Web UI: `http://homeassistant.local:8080` (or your HA host/IP
   with port `8080`). The same port also serves the OPDS catalog at
   `/catalog/v2/entries` and the search API.

## Configuration

| Option           | Default         | Description                                                                                                                                                          |
| ---------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zim_path`       | `/media/kiwiks` | Directory with `*.zim` files, as seen inside the app. Must be under `/media/...` or `/share/...` (i.e. a Home Assistant mount, local or NAS-backed network storage). |
| `threads`        | `4`             | `kiwix-serve` worker threads (1–32).                                                                                                                                 |
| `nodatealiases`  | `false`         | Also serve books under date-less aliases (`..._2024-01` → `...`).                                                                                                    |
| `block_external` | `false`         | Block navigation to external resources from links in ZIM content.                                                                                                    |
| `verbose`        | `false`         | Verbose `kiwix-serve` logging.                                                                                                                                       |

### Adding or removing ZIM files

Copy files in/out of the NAS directory, then **restart** the app. The
library is scanned at startup; no library file maintenance is needed.

### Split ZIM files

For split archives (`file.zimaa`, `file.zimab`, …) place all parts next
to each other and keep the base `file.zim` present; `kiwix-serve`
resolves the parts automatically.

## NAS notes

- The app never writes to the NAS mount (`media`/`share` are mapped
  read-only). All app state lives in `/data` (backed up with HA backups).
- Any storage Home Assistant can mount works: NFS or CIFS shares added
  via **Settings → System → Storage**. The app does not handle NAS
  credentials itself — that is entirely HA's network storage setup.
- Large ZIM files (tens of GB) are fine: they are read in place, never
  copied into the app container.
- If the app fails to start with "Directory not found", the network
  storage is not mounted (NAS offline, credentials changed) or
  `zim_path` is wrong. Check **Settings → System → Storage** first, then
  the app log.

## Ports

- `8080/tcp` → `8080`: kiwix-serve web UI, viewer, search and OPDS API.

## License

Apache-2.0. kiwix-serve itself is GPLv3 (fetched as upstream binary at
image build time from `download.kiwix.org`).
