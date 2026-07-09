# ROAMiQ Cloud

Secure remote access for Home Assistant via Cloudflare Tunnel.

- Website / account: https://cloud.roamiq.com
- Install slug: `roamiq_cloud` (matches folder/name "ROAMiQ Cloud").

## Architectures

This add-on ships pre-compiled native modules (`.so`) for:

| HA arch  | Notes                          |
|----------|--------------------------------|
| aarch64  | Raspberry Pi 4/5 64-bit, most HA Green/Yellow |
| amd64    | Intel/AMD 64-bit (HA OS x86)   |
| armv7    | 32-bit ARM (older Pi / boards) |

The correct binaries are selected automatically at build time from
`lib/<arch>/` via the `BUILD_ARCH` build-arg — one add-on entry works on all
three. `armhf` and `i386` are intentionally NOT offered (no binaries shipped).

## Privacy

The remote-access, pairing, credential-handling and manifest logic ship as
compiled binaries only — there is no readable Python source for those modules
in this add-on. Docstrings and symbol tables are stripped from the binaries.
