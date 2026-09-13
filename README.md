# macos-cloud

A free macOS desktop in the cloud, reachable from any browser — no Mac, no VNC client,
no paid tunnel.

A GitHub Actions **macOS 26 (Tahoe) Intel runner** is used as a throwaway Mac: the job
creates a local admin account, switches on macOS's built-in VNC server, and puts
[noVNC](https://github.com/novnc/noVNC) behind a **Cloudflare quick tunnel**. Open the URL
printed in the job log and you get the real desktop.

## One-time setup

Add one repository secret — **Settings → Secrets and variables → Actions → New repository secret**:

| Secret | Value |
|---|---|
| `VNC_PASSWORD` | **8 characters or fewer** (macOS's legacy VNC auth only reads the first 8) |

That is the only secret needed. No ngrok token: ngrok's free plan now requires a payment
method on file for TCP endpoints, and Cloudflare quick tunnels need no account at all.

## Use it

1. [Run the workflow](https://github.com/prateeknot/macos-cloud/actions/workflows/macos.yml)
   → **Run workflow**.
2. Open the **desktop** job and wait for the `Serve the desktop in a browser` step.
   It prints a line like
   `VNC HOST : https://<random-words>.trycloudflare.com`
3. Open `https://<random-words>.trycloudflare.com/vnc.html?autoconnect=1&resize=scale`.
4. At the macOS login screen use `macuser` / your `VNC_PASSWORD`.
5. When you are done, press **Cancel workflow**. The VM is destroyed.

Only works while the job runs. Nothing persists between runs.

## What you get

| | |
|---|---|
| Runner | `macos-26-intel` — 4 vCPU, **14 GB RAM**, 14 GB SSD |
| OS | macOS 26 (Tahoe), Xcode + Homebrew preinstalled |
| Session | up to **6 hours**, then the VM is recycled |

The arm64 (`macos-26`) runners only have 7 GB RAM — this workflow uses the Intel label
deliberately.

## Things worth knowing

- **Public repo = free and unlimited.** On a private repo macOS runners bill minutes at
  a 10x multiplier (2,000 free minutes → ~3 h of Mac).
- **The job log is public.** Anyone reading it can see the tunnel URL while the job runs,
  so keep the session short and cancel the run when you are finished. The password is
  never printed — it only lives in the repo secret.
- GitHub's Actions terms expect the repo to be for the project you are using the runner
  for. Don't run it as an always-on machine.
- The VM is destroyed with the workflow. Push or upload anything you want to keep before
  you cancel.
- noVNC's copying and pasting is limited, and the desktop is not GPU accelerated — expect
  it to feel like remote desktop over a slow link.

## If you want a real VNC client instead

A native client (RealVNC/TigerVNC) needs a raw TCP tunnel, not the HTTP quick tunnel.
Cloudflare can do it with a named tunnel + `cloudflared access tcp`, but that needs a
domain on your Cloudflare account and a tunnel token. Ask and it can be added as a
second workflow.
