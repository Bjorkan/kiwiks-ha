# Kiwiks Home Assistant app repository

Apps (formerly known as add-ons) for Home Assistant. This repository
contains the **Kiwiks** app: [Kiwix-serve](https://github.com/kiwix/kiwix-tools),
an HTTP server for offline ZIM files (Wikipedia, etc.).

ZIM files stay on your NAS. The NAS is mounted once in Home Assistant
itself (**Settings → System → Storage → Add network storage**), and this
app reads the files through the `/media` or `/share` mount.

[![Open your Home Assistant instance and show the app store with this repository pre-filled.](https://my.home-assistant.io/badges/supervisor_store.svg)](https://my.home-assistant.io/redirect/supervisor_store/?repository_url=https%3A%2F%2Fgithub.com%2FBjorkan%2Fkiwiks-ha)

## Apps

### [Kiwiks (Kiwix-serve)](./kiwiks)

_Serve offline ZIM files from your NAS network storage._

![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg

## Installation

1. Mount your NAS once in Home Assistant: **Settings → System → Storage →
   Add network storage** (usage **Media** or **Share**), e.g. name it
   `kiwiks`.
2. Copy your `*.zim` files into that storage
   (e.g. `//nas/zim` → `/media/kiwiks` or `/share/kiwiks`).
3. Add this repository to Home Assistant (**Settings → Apps → ⋯ →
   Repositories**), install **Kiwiks**, set `zim_path` to your mount
   (e.g. `/media/kiwiks`), and start it.
4. Open the Web UI on port `8080` (e.g. `http://homeassistant.local:8080`).

See [kiwiks/DOCS.md](./kiwiks/DOCS.md) for full documentation.

## Repository layout

```text
kiwiks-ha/
├── repository.yaml
├── README.md
├── .github/workflows/      # multi-arch build + publish (GHCR)
└── kiwiks/                 # the app
    ├── config.yaml         # app metadata, ports, mounts, options schema
    ├── Dockerfile          # installs official kiwix-serve (musl) binaries
    ├── rootfs/etc/services.d/kiwix/  # s6 service: scans ZIMs, starts server
    ├── translations/en.yaml
    ├── apparmor.txt
    ├── DOCS.md
    └── CHANGELOG.md
```

## A note on naming: "Apps" vs "Add-ons"

Home Assistant renamed **Add-ons** to **Apps** (Supervisor UI now shows
**Settings → Apps**). Technically nothing changed for developers: an "app"
is still a Docker container described by `config.yaml` + `Dockerfile`
(+ optional `rootfs/`), published either as a locally-built container or
as pre-built multi-arch images on a registry (GHCR). All official docs
under `developers.home-assistant.io/docs/apps` apply. This repo follows
the current [apps-example](https://github.com/home-assistant/apps-example)
template, builds locally by default, and includes the standard
builder workflow for pre-built GHCR images when you are ready to publish.

## License

Apache-2.0 — see [LICENSE](./LICENSE).
