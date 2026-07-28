# ROAMiQ Cloud

Secure remote access for your Home Assistant via a Cloudflare Tunnel. Pair your
instance to your ROAMiQ Cloud account and reach it from anywhere at
`https://<your-subdomain>.cloud.roamiq.com` — no port forwarding, no static IP.
The add-on also handles encrypted cloud backups, Rewind restore, and temporary
guest access.

## Getting started

1. Install and start the add-on, then open its panel (ROAMiQ Cloud in the
   sidebar).
2. Follow the pairing flow to link this instance to your ROAMiQ Cloud account
   (sign up or sign in at <https://cloud.roamiq.com>). You'll be assigned a
   personal `<subdomain>.cloud.roamiq.com` address.
3. When prompted, confirm the **Home Assistant restart** the add-on requests.
   That restart applies the networking config below so remote access works
   correctly.

## Home Assistant configuration this add-on manages

To make remote access and add-on sign-in flows work over the tunnel, Home
Assistant needs two things set in `configuration.yaml`. **The add-on writes both
automatically on pairing/startup**, then asks you to restart Home Assistant to
apply them. It never overwrites values you've set yourself.

```yaml
homeassistant:
  # Your assigned tunnel address. Lets Home Assistant recognise itself when
  # reached over the tunnel — required for IndieAuth/OAuth add-on sign-in flows
  # (for example Music Assistant's "add music source"). Without it those flows
  # fail with a Cloudflare 502.
  external_url: "https://<your-subdomain>.cloud.roamiq.com"

http:
  # Trust the Cloudflare tunnel reverse proxy so Home Assistant reads the real
  # client address and honours the external URL above.
  use_x_forwarded_for: true
  trusted_proxies:
    - 172.30.32.0/23   # Home Assistant Supervisor add-on network
    - 172.30.33.0/24   # Home Assistant Docker bridge
    - 127.0.0.1
    - ::1
```

### If remote add-on sign-in returns a 502

If opening an add-on page that triggers a Home Assistant sign-in prompt (e.g.
Music Assistant → "add music source") shows a Cloudflare **502 Host Error**,
Home Assistant almost certainly doesn't know its own external URL. Check the
ROAMiQ Cloud add-on log — it prints the exact `external_url` value to add. Then:

1. Add the `homeassistant: external_url` line above (using YOUR subdomain — the
   add-on log and the add-on panel both show it).
2. Restart Home Assistant.

Replace `<your-subdomain>` with the value shown on the ROAMiQ Cloud panel.

## Notes

- **Internet required** — the tunnel and cloud backups need outbound internet.
- **Keep it running / Watchdog** — leave the add-on running so the tunnel stays
  up and scheduled backups continue.

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
