# macos-cloud

A free macOS desktop in the cloud, reachable from any browser.

A GitHub Actions **macOS runner** is used as a throwaway Mac: the job creates a local
admin account, switches on macOS's built-in VNC server, and puts
[noVNC](https://github.com/novnc/noVNC) behind a Cloudflare quick tunnel. Open the URL
printed in the job log and you get the real desktop.

## Use it

1. [Run the workflow](https://github.com/prateeknot/macos-cloud/actions/workflows/macos.yml)
   → **Run workflow**. Set `vnc_password` if you like, then start it.
2. Open the **desktop** job and wait for the `Serve the desktop in a browser` step.
   It prints a link like
   `https://<random-words>.trycloudflare.com/vnc.html?autoconnect=1&...`
3. Open that link. At the macOS login screen use `macuser` / your password.
4. When you are done, press **Cancel workflow**. The VM is destroyed.

Only works while the job runs. Nothing persists between runs.

## What you get

| | |
|---|---|
| Runner | `macos-15-intel` — 4 vCPU, **14 GB RAM**, 14 GB SSD |
| OS | macOS 15 (Sequoia), Xcode + Homebrew preinstalled |
| Session | up to **6 hours**, then the VM is recycled |

The arm64 (`macos-latest`) runners only have 7 GB RAM — this workflow uses the Intel
label deliberately.

## Things worth knowing

- **Public repo = free and unlimited.** On a private repo macOS runners bill minutes at
  a 10x multiplier (2,000 free minutes → ~3 h of Mac).
- **The job log is public.** Anyone reading it can see the tunnel URL and password while
  the job runs. Keep the session short, don't put anything private on the desktop, and
  cancel the run when you are finished.
- GitHub's Actions terms expect the repo to be for the project you are using the runner
  for. Don't run it as an always-on machine.
- noVNC's copying and pasting is limited, and the desktop is not GPU accelerated —
  expect it to feel like remote desktop over a slow link.
