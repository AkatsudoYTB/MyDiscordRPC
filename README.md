<div align="center">

# 🎮 MyDiscordRPC

**Show everything you do - websites and desktop apps - on your Discord profile. Automatically.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
![Windows](https://img.shields.io/badge/Windows-10%20%2F%2011-0078D6?logo=windows&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![Discord](https://img.shields.io/badge/Discord-Rich%20Presence-5865F2?logo=discord&logoColor=white)

[🇫🇷 Français](README.md) · 🇬🇧 English

<!-- 👉 Add a screenshot / GIF of your Discord presence + the extension here: -->
<!-- ![Preview](docs/screenshot.png) -->

</div>

---

Automatically shows on your **Discord** profile what you're doing in your
**browser** (YouTube, SoundCloud, any site) **and** in your **Windows apps**
(FL Studio, VLC, Vocaloid, cmd...), with the real logo, the title, a progress bar,
and more.

- 100 % **free** and **open source** (MIT)
- **No data collection**, everything stays local (except what goes to Discord)
- Windows 10 / 11

> This guide is **for beginners**: every step is explained. Take your time -
> nothing here is complicated.

---

# 📖 Contents

1. [What is it, how does it work?](#1-what)
2. [Step 1 - Install Python](#2-python)
3. [Step 2 - Download MyDiscordRPC](#3-download)
4. [Step 3 - Install the app (dependencies)](#4-deps)
5. [Step 4 - Create your Discord application](#5-discord)
6. [Step 5 - Install Cloudflared (for app logos)](#6-cloudflared)
7. [Step 6 - Edit `settings.json`](#7-settings)
8. [Step 7 - Run the app](#8-run)
9. [Step 8 - Install the browser extension](#9-extension)
10. [Step 9 - Set the options in the extension](#10-options)
11. [Common problems](#11-problems)

---

<a name="1-what"></a>
## 1. What is it, how does it work?

MyDiscordRPC has **two parts** that work together:

- **A browser extension** (Chrome/Edge): it sees which site you're on, which
 video you're watching, etc.
- **A PC application**: it sees which Windows app is open, and talks to Discord
 to show your presence.

```
Browser (extension) ─┐
 ├─► PC application ─► Discord
Windows apps ────────┘
```

So you install **both**. This guide explains how.

---

<a name="2-python"></a>
## 2. Step 1 - Install Python

**What is Python?** It's a free, **official** program, widely used, that runs
programs (including MyDiscordRPC). It is **not a virus**: it's made by a
recognized foundation and downloaded by millions of people. You can install it
with confidence.

1. Go to the official site: **https://www.python.org/downloads/**
2. Click the big **"Download Python 3.x.x"** button (the latest version).
3. Open the downloaded file.
4. **VERY IMPORTANT**: on the installer's first screen, tick the box at the
 bottom **"Add python.exe to PATH"**. ✅
 *(Without it, the commands won't work.)*
5. Click **"Install Now"** and let it finish.

**Check it works**: open the Start menu, type `cmd`, open **Command Prompt**, and
type:
```
python --version
```
You should see `Python 3.x.x`. If you get an error, reinstall and make sure to
tick "Add to PATH".

---

<a name="3-download"></a>
## 3. Step 2 - Download MyDiscordRPC

1. On the project's GitHub page, click the green **"Code"** button →
 **"Download ZIP"**.
2. Unzip it anywhere, e.g. on your Desktop. You get a `MyDiscordRPC` folder
 containing `desktop-app`, `browser-extension`, etc.

*(If you know Git: `git clone <repo-url>`.)*

---

<a name="4-deps"></a>
## 4. Step 3 - Install the app (dependencies)

Let's prepare the PC application. Open **Command Prompt** in the `desktop-app`
folder:
- open the `MyDiscordRPC\desktop-app` folder in File Explorer,
- click the address bar, type `cmd`, and press Enter.

Then run these commands **one at a time**:

```bash
python -m venv .venv
```
```bash
.venv\Scripts\pip install -r requirements.txt
```

The first creates an isolated "environment", the second installs the needed
pieces (Discord, Windows detection...). Wait for it to finish.

---

<a name="5-discord"></a>
## 5. Step 4 - Create your Discord application

**Why?** Discord needs to know "who" shows your presence. For that, you create a
small Discord "application" (free, 2 minutes) and copy its **number
(Application ID)**.

> ℹ️ **No login/OAuth needed** for presence: local Rich Presence only needs this
> number. (PreMiD looks "login-only" because **its makers already created the
> applications and provide the numbers**. If you distribute this project, do the
> same: put your number as the default in
> `desktop-app/config/default_settings.json` (`client_id`) → people who download
> won't have to create anything.)

Steps:
1. Go to **https://discord.com/developers/applications** and log in.
2. Top-right, click **"New Application"**.
3. Give it a name (this name shows **in bold** on your profile, e.g.
 `MyDiscordRPC` or your username), accept the terms, confirm.
4. You land on **"General Information"**. Find **"Application ID"**: a long
 number. Click **"Copy"**.
5. Keep it aside - you paste it in step 6 (or later in the extension).

*(That's all: no bot, no secret, no authorization.)*

---

<a name="6-cloudflared"></a>
## 6. Step 5 - Install Cloudflared (for app logos)

**What's it for?** To show the **logo of your PC apps** (FL Studio, VLC...) on
Discord, Discord must be able to "fetch" the image. Cloudflared creates a small,
temporary public link to your PC for that. It's **free and account-free**.

> This step is **optional**: without it, everything works, but PC apps show
> **without a logo** (just the name + project).

1. Go to **https://github.com/cloudflare/cloudflared/releases** (latest version).
2. Download **`cloudflared-windows-amd64.exe`**
 (`amd64` = 64-bit Windows PC, the normal case).
3. Create a folder **`C:\cloudflared`**.
4. Put the file inside and **rename** it to `cloudflared.exe`
 (final path: **`C:\cloudflared\cloudflared.exe`**).

---

<a name="7-settings"></a>
## 7. Step 6 - Edit `settings.json`

On the **first run** (step 7), a `desktop-app\config\settings.json` file is
created. Open it with Notepad and fill in:

```json
{
 "discord": { "client_id": "PASTE_YOUR_APPLICATION_ID_HERE" },
 "icons": {
 "tunnel": "cloudflared",
 "cloudflared_path": "C:\\cloudflared\\cloudflared.exe"
 }
}
```

- `client_id`: the number copied in step 4.
- `cloudflared_path`: the path from step 5 (keep the **double backslashes** `\\`).

Save. *(You can also set the Discord ID from the extension - see step 9 - without
touching the file.)*

---

<a name="8-run"></a>
## 8. Step 7 - Run the app

1. **Start Discord** (the desktop app, not the browser version - it must be
 running).
2. In Command Prompt (in `desktop-app`), run:
```bash
.venv\Scripts\python.exe run.py
```
3. Leave that window **open** (it runs the app). You should see lines like
 `Connected to Discord`, `WebSocket server...`, and if cloudflared is set up
 `Public icons URL (Cloudflare): https://...`.

To stop: `Ctrl + C` in the window.

---

<a name="9-extension"></a>
## 9. Step 8 - Install the browser extension

1. In your browser, go to **`chrome://extensions`** (or
 **`edge://extensions`** for Edge).
2. Top-right, turn on **"Developer mode"**.
3. Click **"Load unpacked"**.
4. Select the **`browser-extension`** folder (the one with `manifest.json`).
5. The MyDiscordRPC icon (purple ▶ square) appears. The **options open
 automatically** the first time.

*(After every code update, go back to `chrome://extensions` and click the ⟳
button on MyDiscordRPC.)*

---

<a name="10-options"></a>
## 10. Step 9 - Set the options in the extension

Click the extension icon → **⚙ Settings**. There are several tabs:

- **Guide**: a reminder of the steps.
- **Discord**: paste your **Application ID** here (if you didn't put it in
 `settings.json`) → **Save**. The badge at the top turns **green** when the app
 is connected.
- **Indicator**: the small on-page popup - enable/disable, position (6 choices),
 animation, duration, **sound** (including a custom sound file). It appears when
 you open a site and when you switch tabs.
- **Sites**: show sites as **domain** (`youtube.com`), **name** (`YouTube`) or
 **full path**; plus a **list of sites to never show**.
- **Themes**: paste a shareable **theme** (JSON) to customize everything.

**Pick your language** top-right (English / Français).

---

<a name="11-problems"></a>
## 11. Common problems

| Issue | Fix |
|---|---|
| `python is not recognized` | Reinstall Python and tick **"Add to PATH"**. |
| Red badge "App not connected" | Is the app (`run.py`) running? Is Discord running? Correct Application ID? |
| Nothing on Discord | Open your Discord profile (click your avatar); enable *Settings → Activity Privacy → Share your activity*. |
| No app logo | Is cloudflared running? Check the `Public icons URL` line in the logs. |
| Extension stops reacting after an update | `chrome://extensions` → ⟳ on MyDiscordRPC. |
| The "Watch on YouTube" button | Normal: Discord **hides your own buttons**, only your friends see them. |

---

## Going further

- Show **"YouTube" in bold** (instead of your app name):
 [docs/04-nom-par-plateforme.md](docs/04-nom-par-plateforme.md)
- Windows app detection (+ logos):
 [docs/05-applications-windows.md](docs/05-applications-windows.md)
- Extension options & themes:
 [docs/06-options-extension.md](docs/06-options-extension.md)
- Technical architecture: [docs/01-architecture.md](docs/01-architecture.md)

## License

MIT - see [`LICENSE`](LICENSE).
