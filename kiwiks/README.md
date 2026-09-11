# Home Assistant App: Kiwiks

_Serve offline ZIM files (Wikipedia, etc.) from your NAS network storage._

![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg

Runs official [kiwix-serve](https://github.com/kiwix/kiwix-tools)
(`kiwix-tools` 3.8.2, musl binaries from `download.kiwix.org`) on port 8080. ZIM files stay on the NAS — the NAS is mounted once in Home
Assistant itself (**Settings → System → Storage → Add network storage**)
and this app reads them through `/media` or `/share`.
