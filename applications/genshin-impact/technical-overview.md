<h1 style="text-align:center">Genshin Impact — technical overview</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Local install structure and component relationships observed 2026-10-10 (UTC), plus product context from official materials.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Environment](#environment)
- [Developer and publisher](#developer-and-publisher)
- [Official distribution](#official-distribution)
- [Local installation map](#local-installation-map)
- [HoYoPlay components](#hoyoplay-components)
- [Game client components](#game-client-components)
- [Digital signatures](#digital-signatures)
- [Configuration and logs](#configuration-and-logs)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Environment</h2>

| Item | Observation | Confidence |
| --- | --- | --- |
| OS | Windows 11 Pro 64-bit (`10.0.26300`) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| PowerShell | 5.1.26100.x | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Investigation date | 2026-10-10 UTC | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Developer and publisher</h2>

| Item | Detail | Confidence |
| --- | --- | --- |
| Uninstall DisplayName | Genshin Impact / HoYoPlay | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local ARP |
| Publisher string | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local ARP |
| Game biz id | `hk4e_global` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local registry + config |
| Privacy-policy entity | Cognosphere Pte. Ltd. (HoYoverse) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official policy |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Official distribution</h2>

| Channel | Notes | Confidence |
| --- | --- | --- |
| HoYoPlay | Installed at `C:\Program Files\HoYoPlay`; Genshin uninstall string invokes `launcher.exe --uninstall_game=hk4e_global` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Official site | <a href="https://genshin.hoyoverse.com/" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> genshin.hoyoverse.com</a> | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Local installation map</h2>

| Role | Path (sanitised) | Evidence |
| --- | --- | --- |
| HoYoPlay install | `C:\Program Files\HoYoPlay` | ARP + filesystem |
| HoYoPlay version tree | `<HOYOPLAY>\1.18.0.380\` | Filesystem |
| Active game install (HYP `1_0\hk4e_global`) | `<DRIVE>:\Genshin Impact game` | `GameInstallPath` registry |
| Older / alternate game tree | `<OTHER_DRIVE>:\Games\Genshin Impact Game` (smaller, older `GenshinImpact.exe` LastWrite 2023) | Filesystem |
| Standalone HYP record | Another `GameInstallPath` under `HYP\standalone\…\hk4e_global` (different directory) | Registry |
| Per-user HYP data | `%APPDATA%\Cognosphere\HYP\` | Filesystem |
| Unity/player logs dir | `%USERPROFILE%\AppData\LocalLow\miHoYo\Genshin Impact\` | Filesystem (names only) |

> **ℹ️ Note**
>
> Multiple game directories can coexist. HYP `GameInstallPath` for `hk4e_global` is treated as the **active** install. The older alternate tree was not treated as authoritative for version `7.1.0`.

Active game `config.ini` (sanitised keys):

```ini
[General]
game_version=7.1.0
channel=1
sub_channel=0
cps=pcgclid
uapc=[REDACTED_CHANNEL_BLOB]
wpf_version=7.1.0.48145775
```

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">HoYoPlay components</h2>

Observed under `<HOYOPLAY>\1.18.0.380\` (representative):

| File | Observed role (interpretation) | Confidence |
| --- | --- | --- |
| `launcher.exe` / `HYP.exe` | Launcher entrypoints | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Present + product name HoYoPlay |
| `HYUpdater.exe` / `HYUpdaterPlugin.dll` | Update pipeline | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Name + presence |
| `libcef.dll`, `QCefView.dll`, `chrome_elf.dll` | Embedded Chromium/CEF UI | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Filenames + CEF log lines |
| `Qt5*.dll` | Qt UI stack | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Filenames |
| `crashreport.exe` | Crash reporting helper | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Name |
| `telemetry.dll` | Telemetry library | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Filename — behaviour not packet-verified |
| `MiHoYoMTRSDK.dll` | Metrics/telemetry SDK (name) | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Filename |
| `HoYoDeviceFpSDK.dll` | Device fingerprinting SDK (name) | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Filename |
| `sophon.dll`, `hpatchz.exe`, `7z.exe` | Patching / packaging utilities | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> Plausible from names |
| `HYSafeMode.exe` | Safe-mode helper | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> Name only |

HoYoPlay `debug.log` tail (sanitised) showed CEF “CefViewWing” GPU/utility/renderer process launches — consistent with an embedded browser UI. <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local log lines.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Game client components</h2>

Active install siblings (top-level):

| Name | Notes |
| --- | --- |
| `GenshinImpact.exe` | Large (~444 MB) signed Cognosphere binary; FileVersion metadata still shows Unity-era `2017.4.30.0` string |
| `GenshinImpact_Data\` | Unity-style data directory |
| `mhypbase.dll` | Signed Cognosphere |
| `HoYoKProtect.sys` | Anti-cheat driver image co-located with game; SHA-256 **identical** to `C:\WINDOWS\system32\HoYoKProtect.sys` |
| `rtlbase.dll` | Present on active install |
| `pkg_version` / audio pkg manifests | Patch inventory artefacts |
| `driverError.log`, `upload_err.log` | Small local logs (contents not fully published) |

No top-level `UnityPlayer.dll` was found on the **active** `<DRIVE>:` install (unlike the older `<OTHER_DRIVE>:` tree). Exact packaging change rationale: <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown**.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Digital signatures</h2>

| File | Authenticode status | Signer (CN) | Confidence |
| --- | --- | --- | --- |
| `<HOYOPLAY>\launcher.exe` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<HOYOPLAY>\…\HYP.exe` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<HOYOPLAY>\…\HYUpdater.exe` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<HOYOPLAY>\…\crashreport.exe` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<HOYOPLAY>\…\telemetry.dll` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<HOYOPLAY>\…\MiHoYoMTRSDK.dll` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<HOYOPLAY>\…\HoYoDeviceFpSDK.dll` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<GAME>\GenshinImpact.exe` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<GAME>\mhypbase.dll` | Valid | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<SYSTEM32>\HoYoKProtect.sys` | Valid | Microsoft Windows Hardware Compatibility Publisher | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `<GAME>\HoYoKProtect.sys` | Valid | Same publisher; hash matches system copy | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |

> **ℹ️ Note**
>
> A valid publisher signature authenticates origin for Windows trust decisions. It does **not** certify that software is free of bugs, privacy tradeoffs, or undesired behaviour. See [Digital signatures](../../concepts/digital-signatures.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Configuration and logs</h2>

| Artefact | Action taken | Result |
| --- | --- | --- |
| `<HOYOPLAY>\config.ini` | Read keys | `primary_game=hk4e_global`; APM-related switches present |
| Active game `config.ini` | Read keys | `game_version=7.1.0` |
| `%LOCALLOW%\miHoYo\Genshin Impact\output_log.txt` | **Existence only** | Present (~135 KB); **contents not read** (may contain wish webview URLs / secrets) |
| `UidInfo.txt` | Not opened | Present; treated as sensitive |
| HoYoPlay `debug.log` | Tail, sanitised | CEF process noise |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Local observations — [investigation log](investigation-log.md)
- [HoYoPlay help](https://support.hoyoverse.com/hc/en-us/articles/52362516439577-What-should-I-do-if-I-encounter-issues-with-the-HoYoPlay-launcher)
- [Digital signatures](../../concepts/digital-signatures.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Processes and startup](processes-and-startup.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [API and endpoints](api-and-endpoints.md)
