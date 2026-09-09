# headacheron

### Scroll to the bottom of this README for preview images.

A cleaned-up Linux/headless Acheron build for Discord radio streaming, browser control, ICY metadata, and Rich Presence.

This package is branded externally as **headacheron**:


- package folder: `headacheron/`
- executable: `headacheron`
- service: `headacheron.service`
- config: `~/.config/headacheron/`
- source/build: `~/.local/opt/headacheron/`
- installer variables: `HEADACHERON_*`

The underlying Discord/client code is based on the upstream Acheron project.

## Install

```bash
cd ~
unzip headacheron.zip
cd headacheron
chmod +x deploy.sh uninstall.sh
HEADACHERON_PORT=8787 ./deploy.sh
```

The installer will:

1. install Debian/Ubuntu build dependencies if needed;
2. unpack the **Acheron source snapshot embedded in this ZIP** into `~/.local/opt/headacheron`;
3. reuse previously downloaded vendor dependencies, downloading only missing pinned vendor sources;
4. apply the bundled headacheron patch;
5. build the `headacheron` executable;
6. create `~/.config/headacheron/headacheron.env`;
7. create and start the `headacheron.service` systemd user service.

The main Acheron source is no longer cloned from GitHub every time the installer runs.
The embedded snapshot is the user-supplied `acheron-master.zip` at commit
`97064767c7f27aa8c39f47c92dfcb36589fc03a2` and SHA-256:

```text
20c081eec7d4be4c89ab2682bd4d5213f41e17490082ad274c9731fcf2906ff1
```

Acheron's Git submodules are not contained in GitHub's normal source ZIP, so the installer
uses pinned source archives for the few vendor components needed to build headacheron.
Those vendor directories are preserved across updates so they are not re-downloaded unless missing.

Fresh installs start with empty station-specific fields. Configure the stream URL and destination
in the Web UI. Rich Presence stays off until explicitly enabled.

## Current fixes

- Accounts refresh automatically without requiring a browser reload.
- Account cards are reconciled in place, so connection state, active-route state, and token state
  update while account settings are open.
- Open account-settings panels stay open and do not fight the polling refresh.
- Each account has its own **Connect on startup** toggle; multiple saved accounts may connect when the daemon starts.
- Multi-account cards no longer stretch their status badges or Connect/Disconnect/Selected buttons
  when another account's settings panel is expanded.
- Rich Presence is disabled by default on a fresh install.
- No station URL, activity label, artwork, or button values are prefilled.
- Artwork does not link to the stream URL.
- ICY artwork parsing accepts common artwork fields, relative artwork URLs, and artwork-style
  `StreamUrl` metadata.
- Rich Presence artwork is registered through Discord's authenticated `external-assets` endpoint
  using the connected account's normal REST client, then the returned `mp:` media-proxy asset is
  sent in the activity. This matches working gateway-based music presence implementations.
- ICY art is preferred; the configured fallback is proxied in parallel and used whenever live art
  is unavailable or has not been accepted by Discord yet.
- The activity also includes the application ID and `INSTANCE` flag used by Discord client RPC activities.
- Up to two optional Rich Presence buttons are supported.
- Current route, Now Playing, forms, cards, and mobile layouts are responsive.

## Service commands

```bash
systemctl --user status headacheron
systemctl --user restart headacheron
systemctl --user stop headacheron
systemctl --user start headacheron
journalctl --user -u headacheron -f
```

To keep the user service running after logout/reboot:

```bash
sudo loginctl enable-linger "$USER"
```

## Web token

The installer prints the generated token once. To show it again:

```bash
sed -n 's/^HEADACHERON_WEB_TOKEN=//p' ~/.config/headacheron/headacheron.env
```

With port 8787, open:

```text
http://SERVER_LAN_IP:8787
```

## Installer options

```bash
HEADACHERON_PORT=8787 ./deploy.sh
HEADACHERON_BIND_ADDRESS=127.0.0.1 ./deploy.sh
HEADACHERON_SKIP_PACKAGES=1 ./deploy.sh
HEADACHERON_SKIP_SERVICE=1 ./deploy.sh
HEADACHERON_INSTALL_DIR=/absolute/path ./deploy.sh
```

## Uninstall

Keep settings/data:

```bash
./uninstall.sh
```

Remove headacheron completely:

```bash
./uninstall.sh --purge
```

Also remove old `acherond` / `~/acheron-headless` installs:

```bash
./uninstall.sh --purge --legacy
```

The uninstaller intentionally does not remove system packages because they may be used by other software.

## Security

The Web UI is HTTP, not HTTPS. Keep it on a trusted LAN, private VPN, SSH tunnel, or HTTPS reverse
proxy. Do not expose the raw control port directly to the public internet.

<img width="2554" height="1300" alt="Screenshot 2026-09-08 213147" src="https://github.com/user-attachments/assets/0ee443dd-8398-4414-b35f-3286f96b0a99" />
<img width="2558" height="1304" alt="Screenshot 2026-09-08 213140" src="https://github.com/user-attachments/assets/b3bcee17-fa09-4d84-bf2b-09672f47642e" />
<img width="2556" height="1307" alt="Screenshot 2026-09-08 213134" src="https://github.com/user-attachments/assets/87f9d6bd-6e6f-47f9-8237-1a11f07e587e" />
<img width="2557" height="1311" alt="Screenshot 2026-09-08 213128" src="https://github.com/user-attachments/assets/950dd3f6-2848-4b97-9511-648b6b60833e" />
<img width="2553" height="1305" alt="Screenshot 2026-09-08 213121" src="https://github.com/user-attachments/assets/91d72625-980c-49a4-b2ae-10c8a16fe698" />
<img width="2547" height="1343" alt="Screenshot 2026-09-08 213112" src="https://github.com/user-attachments/assets/a96960ac-f5e9-4550-a3b3-ab86cc32ba6a" />
