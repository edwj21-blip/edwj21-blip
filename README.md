<div align="center">

# 🤖 J.A.R.V.I.S. – Phone Control Center
### Personal AI Desktop Agent · Android Phone Control · Laptop Automation

![Status](https://img.shields.io/badge/Status-In_Progress-orange?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=django&logoColor=white)
![Android](https://img.shields.io/badge/Android_ADB-3DDC84?style=for-the-badge&logo=android&logoColor=white)
![Windows](https://img.shields.io/badge/Windows_10%2F11-0078D6?style=for-the-badge&logo=windows&logoColor=white)

</div>

An intelligent, secure, cyberpunk-styled **Personal AI Desktop Agent** built with **Django**, **Python**, **Android Debug Bridge (ADB)**, **scrcpy**, and **WhatsApp Desktop & Android automation**.

> 🚧 **Project status: work in progress.** JARVIS is under active development. Features below are implemented in code, but not everything has been tested on every device, and things may change or break between commits. Feedback and ideas are welcome.

> **Target smartphone:** OPPO A15s (Model `CPH2179`, Android 10, ColorOS 7.2)
> **Design goals:** 100% local execution · no hardcoded credentials · zero lock-screen bypass

---

## 📑 Table of Contents
- [Architecture Overview](#-architecture-overview)
- [Android Phone Control Center](#-android-phone-control-center-oppo-a15s)
- [Natural Voice Commands](#️-natural-voice-commands)
- [OPPO A15s Setup Guide](#️-target-oppo-a15s-cph2179-setup-guide)
- [Installation & Running](#-installation--running-jarvis)
- [Testing](#-automated-testing)
- [Security Architecture](#-security-architecture)
- [Feature Status](#-feature-status)
- [Known Limitations](#️-known-technical-limitations-android-10--coloros-72)
- [Roadmap](#-roadmap)
- [Disclaimer](#️-disclaimer)
- [About the Author](#-about-the-author)

---

## 📋 Architecture Overview

The former college attendance portal has been **removed** from the project. In its place is the **Android Phone Control Center**, integrated into the JARVIS Neural HUD and accessible via voice, the web dashboard, and a standalone terminal.

```
┌────────────────────────────────────────────────────────────────────────┐
│                   J.A.R.V.I.S. NEURAL HUD & CORE                       │
│     Web Speech Recognition  •  British Voice TTS  •  Cyberpunk UI      │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
         ┌──────────────────────────┴──────────────────────────┐
         ▼                                                     ▼
┌──────────────────────────────┐              ┌──────────────────────────────┐
│  ANDROID PHONE CONTROL       │              │  WINDOWS LAPTOP AUTOMATION   │
│  (OPPO A15s / Android 10)    │              │  (Local Safe Subprocess)     │
├──────────────────────────────┤              ├──────────────────────────────┤
│ • USB & Wireless ADB         │              │ • WhatsApp Desktop (UIA)     │
│ • Wake & Lock Screen Help    │              │ • Safe Allowlist App Launch  │
│ • scrcpy Screen Mirroring    │              │ • System Diagnostics & Vol   │
│ • Safe File Manager          │              │ • Workstation Locking        │
│ • Android WhatsApp Intent    │              │ • Academic Web Search        │
│ • Google Find My Device      │              │ • Desktop Screenshots        │
└──────────────────────────────┘              └──────────────────────────────┘
```

**Project layout**

| Folder / file | Purpose |
| :--- | :--- |
| `jarvis_project/` | Django project settings and URL config |
| `jarvis_core/` | Main app: agent engine, views, models, templates, HUD static files |
| `desktop_agent/` | Android controller, WhatsApp controller, system controller, terminal runner |
| `run_jarvis.bat` | 1-click Windows launcher |
| `seed_device_data.py` | Seeds the OPPO A15s device profile |
| `.env.example` | Template for environment variables (never commit your real `.env`) |

---

## 📱 Android Phone Control Center (OPPO A15s)

### Section A: Connection Manager
- **Dual Connection Modes**: Fast USB ADB and Wireless ADB (TCP/IP).
- **Android 10 / ColorOS 7.2 Workflow**: Because Android 10 lacks native Android 11+ pairing codes, JARVIS guides the standard TCP/IP workflow: plug in via USB once, activate port 5555 (`adb tcpip 5555`), unplug USB, and connect over Wi-Fi (`adb connect <ip>:5555`).
- **Telemetry Card**: Real-time status badge (`READY`, `UNAUTHORIZED`, `DISCONNECTED`, or `NO_ADB`), hardware serial, battery level, and connection type.
- **Troubleshooting Tips**: Guidance for ColorOS permission prompts, RSA key approval, and MTP modes.

### Section B: Wake & Lock-Screen Assistance
- **Wake Phone Screen**: Sends standard Android keyevents (`KEYCODE_WAKEUP 224` or `KEYCODE_POWER 26`).
- **Present Lock Screen**: Performs an upward swipe (360, 1350 to 360, 400) to present the PIN/pattern keypad for user authentication.
- **Strict Android Security Guarantee**: **Zero lock-screen bypass**. JARVIS never enters PINs, passwords, or biometrics. All authentication stays on the device hardware.

### Section C: App Launcher & System Navigation
- **Approved App Allowlist**: Direct launch of WhatsApp, Google Chrome, YouTube, Google Maps, System Settings, OPPO Camera, Phone Dialer, and ColorOS Calculator/File Manager.
- **Hardware Navigation**: Simulates `Home` (`KEYCODE_HOME 3`), `Back` (`KEYCODE_BACK 4`), `Recent Apps` (`KEYCODE_APP_SWITCH 187`), `Volume Up` (24), and `Volume Down` (25).
- **Touch & Text Automation**: Safe coordinate taps, swipes, and text injection with `%s` space escaping.

### Section D: Screen Mirroring (scrcpy)
- **Low-Latency Mirroring**: Integrates `scrcpy` with flags tuned for the OPPO A15s 720x1600 screen (`--max-size 1024`, `--max-fps 60`, `--stay-awake`).
- **Honest State Reporting**: Never pretends a live screen is running if scrcpy is offline. Shows installation guidance if scrcpy is missing.
- **Instant Snapshots**: Uses `adb exec-out screencap -p` to take phone screenshots and show them in the HUD.

### Section E: File Manager (Push & Pull)
- **Bidirectional Transfer**: Push files from Windows to the phone (`/sdcard/Download/`) or pull files from the phone to the PC.
- **Directory Browser**: Browses `/sdcard/` directories.
- **Overwrite Protection**: If a file already exists, the transfer halts until explicit overwrite authorization is given.
- **Safe Deletion**: File deletion requires explicit confirmation (`confirmed=True`).

### Section F: WhatsApp Assistance
- **WhatsApp Desktop Automation**: Windows UI Automation for desktop chats and multi-group messaging.
- **Android WhatsApp Integration**: Fills in recipient and message drafts using standard Android intent URLs (`https://api.whatsapp.com/send?phone=<phone>&text=<message>`).
- **Human-in-the-Loop Confirmation**: Prepares drafts and waits for explicit confirmation before sending. Never claims a message was sent unless verified.

### Section G: Device Status & Telemetry
- **Battery**: Percentage, charging state, temperature (°C), and health via `dumpsys battery`.
- **Storage**: Total, used, and free GB on `/data` via `df`.
- **Network**: Local Wi-Fi IPv4 address via `ip route` / `ip addr show wlan0`.
- **Activity Log**: Timestamped action audit log stored in SQLite.
- **Emergency Disconnect**: Red button that halts screen mirroring, severs wireless ADB sessions, and resets the ADB daemon.

### Section H: Lost-Phone Recovery
- **Google Find My Device Shortcut**: Direct access to `https://android.com/find`.
- **Honest Disclosure**: Explains that GPS location, ringing, locking, or erasing needs Google services, active data/Wi-Fi, and power, and that ADB is a local protocol that cannot track a disconnected or lost phone.

---

## 🎙️ Natural Voice Commands

Speak or type these commands to JARVIS:

| Voice Command | Action Taken | Handler |
| :--- | :--- | :--- |
| *"JARVIS, connect my phone."* | Queries ADB devices; connects via saved wireless IP if configured. | `JarvisAgent._handle_phone_query()` → `android_controller.get_device_overview()` |
| *"JARVIS, wake my phone."* | Turns on the display and shows the lock screen for normal authentication. | `android_controller.show_lock_screen()` (`KEYCODE_WAKEUP` + swipe) |
| *"JARVIS, open WhatsApp."* | Launches or focuses WhatsApp Desktop. | `whatsapp_controller.launch_app()` |
| *"JARVIS, open WhatsApp on phone."* | Launches Android WhatsApp on the OPPO A15s. | `android_controller.launch_app('whatsapp')` |
| *"JARVIS, show my phone screen."* | Launches the `scrcpy` window or takes an instant snapshot. | `android_controller.start_scrcpy()` |
| *"JARVIS, tell me my battery level."* | Reports laptop battery plus connected phone battery status. | `SystemController.get_battery_status()` + phone telemetry |
| *"JARVIS, send this file to my phone."* | Transfers the latest screenshot or file to `/sdcard/Download/`. | `android_controller.push_file()` |
| *"JARVIS, disconnect my phone."* | Halts mirroring, closes the wireless session, resets the server. | `android_controller.emergency_disconnect()` |
| *"JARVIS, find my lost phone."* | Opens Google Find My Device with a disclaimer. | `android_controller.get_lost_phone_info()` |
| *"JARVIS, lock my phone."* | Turns off the screen and locks the phone display. | `android_controller.lock_phone_screen()` (`KEYCODE_SLEEP`) |

---

## ⚙️ Target OPPO A15s (CPH2179) Setup Guide

Follow these steps once to configure your OPPO A15s (ColorOS 7.2 / Android 10).

### 1. Enable Developer Options
1. Go to **Settings** > **About Phone**.
2. Tap **Version / Build Number** **7 times** until you see *"You are now in Developer mode!"*.

### 2. Enable USB Debugging
1. Go to **Settings** > **Additional Settings** > **Developer Options**.
2. Turn **Developer Options** ON.
3. Scroll down and enable **USB Debugging**.
4. **ColorOS-specific:** enable **Disable Permission Monitoring**. *(This lets simulated taps and key injection work without ColorOS security popups blocking automation.)*

### 3. Connect via USB
1. Plug the phone into your PC with a data-capable USB cable.
2. Pull down the notification shade, tap the USB notification, and select **Transfer Files (MTP)**. *(Not "Charge Only".)*
3. On the phone, when you see **"Allow USB debugging?"**, tick **"Always allow from this computer"** and tap **Allow**.

### 4. Enable Wireless ADB (Android 10 TCP/IP)
1. Make sure the PC and phone are on the **same Wi-Fi network**.
2. In the JARVIS Phone Control Center, click **"Enable Wireless Mode (TCP/IP)"**.
3. Once port 5555 is confirmed open, unplug the USB cable.
4. Click **"Connect Wireless"** and enter the phone's Wi-Fi IP address.

---

## 🚀 Installation & Running JARVIS

### Prerequisites
- Windows 10 or 11 (64-bit)
- Python 3.10 – 3.13
- **Android Platform Tools (ADB)**
  - `winget install Google.PlatformTools`
  - or download from [Android SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools)
- **scrcpy (screen mirroring)**
  - `winget install Genymobile.scrcpy`
  - or download from [scrcpy releases](https://github.com/Genymobile/scrcpy/releases)

### Step 0: Configure environment variables
Copy the template and set your own secret key. **Never commit `.env`.**
```powershell
copy .env.example .env
```
Generate a key:
```powershell
python -c "from django.core.management.utils import get_random_secret_key as g; print(g())"
```
Set these in your shell or `.env` (see `.env.example`):
```text
DJANGO_SECRET_KEY=<your generated key>
DJANGO_DEBUG=True
DJANGO_ALLOWED_HOSTS=127.0.0.1,localhost
```
If `DJANGO_SECRET_KEY` is not set, a dev-only fallback key is used and the server refuses to start with `DEBUG` off.

### Option A: 1-Click Launch
Double-click:
```text
run_jarvis.bat
```
It installs dependencies, applies migrations, starts the local server, and opens `http://127.0.0.1:8000`.

### Option B: Manual launch
1. Open PowerShell in the project folder (the one containing `manage.py`):
   ```powershell
   cd Jarvis-Phone-Control-Center
   ```
2. Install packages:
   ```powershell
   python -m pip install -r requirements.txt
   ```
3. Run migrations and seed the OPPO A15s profile:
   ```powershell
   python manage.py makemigrations
   python manage.py migrate
   python seed_device_data.py
   ```
4. Start the server:
   ```powershell
   python manage.py runserver 127.0.0.1:8000
   ```
5. Open your browser:
   - **HUD Dashboard**: `http://127.0.0.1:8000`
   - **Phone Control Center**: `http://127.0.0.1:8000/phone/`

### Option C: Standalone desktop voice console
Run JARVIS in your terminal with offline speech:
```powershell
python desktop_agent/desktop_runner.py
```

> The local SQLite database (`db.sqlite3`) and `media/` screenshots are created on your machine at runtime. They are git-ignored and never uploaded.

---

## 🧪 Automated Testing

The project includes a test suite that uses a mock backend for phone hardware and WhatsApp automation.

```powershell
python manage.py test
```

Test modules:
- `AttendanceRemovalTests`: checks the old attendance models and the `/attendance/` route are gone.
- `AndroidControllerUnitTests`: device overview states, wake/lock help, app allowlist, navigation keys, touch/text automation, scrcpy start/stop, screenshots, file push/pull/delete confirmation.
- `JarvisVoiceCommandTests`: checks voice commands are routed to their handlers.
- `AndroidApiSecurityAndEndpointsTests`: local-IP restriction (remote IPs get HTTP 403), status APIs, quick actions, WhatsApp drafting.
- `SystemControllerTests`: CPU, RAM, battery metrics and the execution allowlist.
- `WhatsAppAutomationTests`: desktop automation, contact matching, and broadcast queues.

Because the project is still in progress, run the suite after pulling changes and report anything that fails.

---

## 🔒 Security Architecture

1. **Subprocess Argument Lists**: No shell concatenation (`shell=False`) in ADB or system execution, to prevent command injection.
2. **Localhost Restriction**: Phone automation endpoints verify the caller is `127.0.0.1` / loopback (`is_local_request`). Remote requests get `HTTP 403 Forbidden`.
3. **Zero Credential Handling**: PINs, patterns, passwords, and biometrics are never stored, logged, or bypassed.
4. **Boundary Isolation**: File push, pull, and delete are confined to `/sdcard/` (`/storage/emulated/0/`). Access to `/system/`, `/data/`, or root partitions is blocked.
5. **Confirmation Safeguards**: File overwrite, file deletion, and WhatsApp sending all require explicit confirmation.
6. **No Secrets in the Repo**: The Django `SECRET_KEY`, `DEBUG`, and `ALLOWED_HOSTS` come from environment variables. `.gitignore` excludes `.env`, databases, screenshots, and `media/`.

See [SECURITY.md](SECURITY.md) for more.

---

## 📊 Feature Status

All features below are implemented in code. The project is in progress, so treat them as **working draft** until you have verified them on your own device.

| Feature | Status | Details |
| :--- | :---: | :--- |
| **USB ADB Connection** | 🟢 Implemented | Hardware discovery, status monitoring, serial identification. |
| **Wireless ADB (TCP/IP)** | 🟢 Implemented | Android 10 workflow via port 5555. |
| **Wake & Lock Screen Help** | 🟢 Implemented | Wakes display and swipes to show the lock screen, without bypassing the PIN. |
| **App Launcher & Navigation** | 🟢 Implemented | WhatsApp, Chrome, YouTube, Maps, Settings; Home, Back, Recents. |
| **Touch & Text Automation** | 🟢 Implemented | Tap, swipe, and text entry with safe character escaping. |
| **Screen Mirroring (scrcpy)** | 🟢 Implemented | Dedicated scrcpy window; shows setup instructions if scrcpy is missing. |
| **Screen Snapshots** | 🟢 Implemented | `adb exec-out screencap -p`, saved to `media/screenshots/phone/`. |
| **File Push / Pull** | 🟢 Implemented | Windows ⇄ phone storage with overwrite confirmation. |
| **File Deletion** | 🟢 Implemented | Confined to `/sdcard/`, needs `confirmed=True`. |
| **Android WhatsApp** | 🟢 Implemented | Intent URL draft loading, explicit confirmation before sending. |
| **WhatsApp Desktop** | 🟢 Implemented | Desktop automation for individual and broadcast messaging. |
| **Device Diagnostics** | 🟢 Implemented | Battery, temperature, charging state, free storage, Wi-Fi IP. |
| **Emergency Disconnect** | 🟢 Implemented | Stops scrcpy, severs wireless ADB, resets the ADB server. |
| **Lost-Phone Recovery** | 🟢 Implemented | Find My Device shortcut with an honest disclaimer. |
| **Voice Commands** | 🟡 In progress | 10 commands wired to handlers; more phrasing variants being added. |

---

## ⚠️ Known Technical Limitations (Android 10 / ColorOS 7.2)

1. **No native wireless pairing (Android 11+)**: Android 10 doesn't support `adb pair <ip>:<port> <code>`. Wireless ADB requires a USB connection once per reboot to enable TCP/IP mode (`adb tcpip 5555`).
2. **ColorOS permission popups**: Touch injection may need **Disable Permission Monitoring** in ColorOS Developer Options.
3. **Lost-phone offline tracking**: ADB cannot track a phone that is disconnected or powered off. Use Google Find My Device.
4. **scrcpy dependency**: Screen mirroring needs `scrcpy` on the Windows PC. Without it, JARVIS falls back to instant snapshots and shows install steps.
5. **Windows only**: Desktop automation uses Windows UI Automation (`pywinauto`, `comtypes`).

---

## 🗺️ Roadmap

- [ ] Test on more Android devices and versions
- [ ] Broader and more natural voice command phrasing
- [ ] Cleaner leftover code from the removed attendance portal (`seed_academic_data.py`, old migrations)
- [ ] More dashboard polish and error messages
- [ ] Add a license
- [ ] Screenshots / demo GIF for this README

---

## ⚖️ Disclaimer

JARVIS is a personal project built for controlling **your own devices**. It does not bypass lock screens, PINs, passwords, or biometrics, and must not be used to access devices or accounts you do not own or have permission to use. Use at your own risk.

---

## 👨‍💻 About the Author

**Edwin Joseph**: Python / Django developer and MCA student from Alappuzha, Kerala, India. I build backend and AI-powered web applications, and I'm open to internships and entry-level developer roles.

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-092E20?style=flat-square&logo=django&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=flat-square&logo=flask&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat-square&logo=scikitlearn&logoColor=white)
![AWS](https://img.shields.io/badge/AWS_EC2-FF9900?style=flat-square&logo=amazonaws&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)

**Other projects**
- 🌊 [FloodWatch Kerala](https://github.com/edwj21-blip/floodwatch-kerala): flood early-warning and shelter management portal (Django, AWS EC2)
- 🌧️ [RainCast](https://github.com/edwj21-blip/raincast-rainfall-prediction): rainfall prediction web app (Flask, scikit-learn)
- 🧑‍💻 [Portfolio](https://github.com/edwj21-blip/portfolio): personal website

<div align="center">

<a href="https://edwin-03-portfolio.vercel.app/"><img src="https://img.shields.io/badge/Portfolio-Visit-000000?style=for-the-badge&logo=vercel&logoColor=white" alt="Portfolio"/></a>
<a href="https://www.linkedin.com/in/edwin-joseph-977b0043b"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/></a>
<a href="mailto:edwj21@gmail.com"><img src="https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white" alt="Email"/></a>

</div>
