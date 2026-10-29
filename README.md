# ⬢ PodDeck

A friendly web dashboard for Podman, built for Raspberry Pi. Open it in any browser — on the Pi, your laptop or your phone.

![Containers](screenshots/containers.png)

| Overview | Live logs |
|---|---|
| ![Overview](screenshots/overview.png) | ![Logs](screenshots/logs.png) |
| **Images** | **New container** |
| ![Images](screenshots/images.png) | ![New container](screenshots/new-container.png) |
| **Light theme** | **On your phone** |
| ![Light theme](screenshots/containers-light.png) | ![Phone](screenshots/phone.png) |

## What it does

- **Overview** – your Pi's CPU, memory, temperature and storage, plus how many containers are running
- **Containers** – start, stop, restart, pause or remove them, see live CPU/RAM per container, click a port to open the app, and watch logs live
- **Images** – pull new images, delete old ones, one-click clean-up of unused ones
- **New container** – a simple form (image, name, ports, folders, settings, restart rule) plus quick-starts: Nginx web server, Uptime Kuma, File Browser, IT Tools and a Hello World test

No extra Python packages, no internet needed for the dashboard itself — just Python 3 (already on Raspberry Pi OS) and Podman.

## Install (recommended)

Copy the `poddeck` folder to the Pi, then in a terminal:

```bash
cd poddeck
bash install.sh
```

It installs Podman if missing, asks you to pick a password, sets PodDeck to start on every boot, and puts a **PodDeck** shortcut on the Desktop. Run it again any time to change the password (press Enter to keep it). It checks that PodDeck really started before it finishes.

Then open **http://localhost:8070** on the Pi, or **http://&lt;pi-ip&gt;:8070** from another device.

## Just try it (no install)

```bash
python3 poddeck.py
```

Press Ctrl+C to stop. Add a password with `PODDECK_PASSWORD=mypassword python3 poddeck.py`.

## Settings

| Variable | Default | What it does |
|---|---|---|
| `PODDECK_PORT` | `8070` | Web port |
| `PODDECK_HOST` | `0.0.0.0` | Use `127.0.0.1` to allow only the Pi itself |
| `PODDECK_PASSWORD` | *(none)* | Require a sign-in |
| `PODDECK_PASSWORD_FILE` | *(none)* | Read the password from a file (what install.sh uses, in `~/.config/poddeck/password`) |

## Good to know

- PodDeck runs **rootless** as your normal user, so it manages *your* containers (the same ones `podman ps` shows without sudo).
- Images must be built for **ARM64** to run on a Pi. The quick-starts all are.
- Short image names like `nginx` or `louislam/uptime-kuma` are looked up on Docker Hub automatically.
- After a reboot, containers set to **Always** or **Unless I stop it** come back on their own. One you stopped yourself stays off.
- Rootless containers can't use ports below 1024 — use 8080 instead of 80, and so on.
- Anyone who can open PodDeck can run containers on your Pi, so set a password if it's on a shared network.
- Removing PodDeck (`bash uninstall.sh`) never deletes your containers or images.

## What's new in 1.1

- **Starts on boot from My Apps.** The service broke when PodDeck lived in a folder with a space (like `My Apps`); the path is now quoted. Clicking the icon spots an old broken setup and repairs it, keeping your password.
- **Any password works.** Passwords with spaces, `%`, `$` or quotes were cut short or broke the service. They're now kept in a private file.
- **Short image names work** on Raspberry Pi OS (`nginx` → `docker.io/library/nginx`).
- **"Unless I stop it" survives reboots.** Podman's own restart service only brings back "Always" containers; PodDeck now handles the rest once per boot.
- **Clear messages** for ports under 1024, busy ports, taken names, and non-ARM images.
- **Retry after a failed create** works. A busy port no longer leaves behind a container that blocks the name.
- The installer no longer replaces My Apps' desktop icon, and it gives Podman the user IDs it needs if an account was renamed.
