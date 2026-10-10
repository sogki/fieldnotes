<h1 style="text-align:center">Discord — technical overview</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Local Stable desktop structure observed 2026-10-10 UTC, aligned with Discord’s published Electron/Chromium statements.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Environment](#environment)
- [Installation layout](#installation-layout)
- [Executables and modules](#executables-and-modules)
- [Digital signatures](#digital-signatures)
- [Roaming profile artefacts](#roaming-profile-artefacts)
- [Other Discord-related installs](#other-discord-related-installs)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Environment</h2>

| Item | Observation | Confidence |
| --- | --- | --- |
| OS | Windows 11 Pro 64-bit | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Stable version | `1.0.9261` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Active app dir | `%LOCALAPPDATA%\Discord\app-1.0.9261\` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Roaming data | `%APPDATA%\discord\` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Installation layout</h2>

Squirrel.Windows-style per-user install:

| Path | Role | Confidence |
| --- | --- | --- |
| `%LOCALAPPDATA%\Discord\Update.exe` | Updater / launch shim (`--processStart Discord.exe`) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local + SquirrelSetup.log |
| `%LOCALAPPDATA%\Discord\app-1.0.9261\` | Current application payload | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `%LOCALAPPDATA%\Discord\packages\` | Update packages | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Directory present |
| `%LOCALAPPDATA%\Discord\SquirrelSetup.log` | Updater log | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `%LOCALAPPDATA%\Discord\Discord_updater_*.log` | Additional updater logs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Root `Discord.exe` (small) beside `Update.exe` | Stub/shortcut target style binary (~630 KB) vs large app `Discord.exe` (~220 MB) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Both present |

Vendor context: Discord describes desktop as Electron with Chromium memory limits and an automatic updater ([engineering post](https://discord.com/blog/how-discord-seamlessly-upgraded-millions-of-users-to-64-bit-architecture)).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Executables and modules</h2>

<h3 style="text-align:center">App directory highlights</h3>

| Name | Notes |
| --- | --- |
| `Discord.exe` | Main Electron host (~220 MB), FileVersion 1.0.9261 |
| `ffmpeg.dll`, `libEGL.dll`, `libGLESv2.dll` | Media / ANGLE (Chromium stack) |
| `updater.node`, `updater_launcher.exe` | In-app update helpers |
| `discord_wer.dll` | Windows Error Reporting related naming |
| `resources\`, `locales\` | Packaged UI resources |

<h3 style="text-align:center">Native modules (`modules\`)</h3>

Directory names observed (versions suffix `-1` / `-2`):

`discord_desktop_core`, `discord_voice`, `discord_rpc`, `discord_krisp`, `discord_overlay2`, `discord_desktop_overlay`, `hook`, `media`, `notifications`, `spellcheck`, `cloudsync`, `dispatch`, `erlpack`, `game_utils`, `utils`, `zstd`, …

| Interpretation | Label |
| --- | --- |
| Modular native addons for voice, RPC, overlay, noise suppression (Krisp), etc. | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Strongly supported by names + known product features |
| Exact behaviour of `discord_hook` | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not reverse-engineered |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Digital signatures</h2>

| File | Status | Signer CN | Confidence |
| --- | --- | --- | --- |
| `Update.exe` | Valid | Discord Inc. (Product “Update”; Company metadata “GitHub” — Squirrel heritage) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `app-…\Discord.exe` | Valid | Discord Inc. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `ffmpeg.dll`, `libEGL.dll`, `libGLESv2.dll`, `vk_swiftshader.dll` | Valid | Discord Inc. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `d3dcompiler_47.dll` | Valid | Microsoft Corporation | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |

Signature ≠ safety certification. See [digital signatures](../../concepts/digital-signatures.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Roaming profile artefacts</h2>

`%APPDATA%\discord\` contains typical Chromium/Electron user-data classes:

| Name | Notes | Action |
| --- | --- | --- |
| `Local Storage`, `Session Storage`, `IndexedDB` | May hold session material | **Not opened** |
| `Cache`, `Code Cache`, `GPUCache` | Caches | Names only |
| `Crashpad` | Crash reporting | Names only |
| `sentry\` (`queue\`, `scope_v3.json`) | Sentry client-side artefacts | Names/sizes only — **not** uploaded or parsed deeply |
| `logs\` | Client logs | Not exported |
| `settings.json` | Window/audio/HW flags (no token fields observed among keys) | Keys reviewed; device-ish values redacted in log |

`settings.json` included hardware acceleration and experimental audio flags, plus a developer-tools related boolean key name. No auth tokens appeared in the top-level key list.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Other Discord-related installs</h2>

| Product | Version | Publisher | Notes |
| --- | --- | --- | --- |
| Discord Canary | 1.0.1099 | Discord Inc. | Discovered via Uninstall/ARP entry; typical layout uses `%LOCALAPPDATA%\DiscordCanary`. Not in primary scope; **not** asserted as running at snapshot time |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Local filesystem / Authenticode / ARP — [investigation log](investigation-log.md)
- [Discord 64-bit blog](https://discord.com/blog/how-discord-seamlessly-upgraded-millions-of-users-to-64-bit-architecture)
- [Squirrel.Windows install docs](https://github.com/Squirrel/Squirrel.Windows/blob/master/docs/using/install-process.md) (generic layout)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Processes and startup](processes-and-startup.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [API and endpoints](api-and-endpoints.md)
