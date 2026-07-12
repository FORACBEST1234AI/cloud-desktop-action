# 🖥️ Cloud Desktop on GitHub Actions

Ephemeral Ubuntu XFCE desktop, streamed to your browser at **near-native FPS** — no noVNC lag.

| | |
|---|---|
| **Streaming** | [KasmVNC](https://github.com/kasmtech/KasmVNC) (WebP / H.264 over WebSocket) |
| **Tunnel** | Cloudflare Quick Tunnel (`*.trycloudflare.com`) — no signup |
| **Desktop** | Ubuntu 22.04 + XFCE 4 + Firefox |
| **Session** | Up to 5 hours (workflow timeout ≈ 6 h) |
| **Downloads** | Second tunnel serves `~/Downloads` at full runner bandwidth |

## Why not noVNC?

`noVNC` decodes raw framebuffer updates in JS on the main thread — that's why it feels laggy and low-FPS. **KasmVNC** ships its own web client that:

- Streams **WebP** or **H.264** rectangles (10–20× less bandwidth)
- Decodes on a worker thread with `<canvas>` / `WebCodecs`
- Ships adaptive quality + 60 fps target out of the box

Result: smooth cursor, playable video, low CPU.

## Usage

1. Go to the **Actions** tab of this repo
2. Pick **Cloud Desktop (KasmVNC + Cloudflare Tunnel)** → **Run workflow**
3. (Optional) Set `session_hours`, `resolution`, `password`
4. Open the run → the **Summary** shows your URL, username (`kasm`) and password
5. Log in — that's it.

### Fast downloads

Anything saved into `~/Downloads` inside the desktop is exposed at the second
`trycloudflare.com` URL printed in the run summary. Direct HTTP → uses the full
GitHub-runner egress (~1 Gbit/s), much faster than downloading through the VNC
"file transfer" panel.

## Repo layout

```
.
├─ .github/workflows/
│   ├─ desktop.yml   # the actual desktop runner (manual dispatch)
│   └─ test.yml      # lint / syntax / URL reachability checks
└─ README.md
```

## Local validation

The `test.yml` workflow runs on every push:

- `yamllint` + `actionlint` — catches workflow errors before you dispatch
- Shell / Python syntax check
- `HEAD` request to the KasmVNC + cloudflared release URLs so we know a
  future upstream removal won't break the desktop workflow

## Legal / fair use

GitHub Actions minutes are for CI. Use this for short-lived, personal
exploration only — don't mine crypto, don't leave sessions idle, and respect
[GitHub's Acceptable Use Policies](https://docs.github.com/en/site-policy/acceptable-use-policies).
