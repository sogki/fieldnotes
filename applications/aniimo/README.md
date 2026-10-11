<h1 style="text-align:center"><img src="../../assets/branding/icon-game.svg" alt="" width="40" height="40" style="vertical-align:middle;margin-right:10px">Aniimo</h1>

<a href="../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Applications</a> · <a href="../../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes</a>

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

Two hands-on investigations of Aniimo on 2026-10-11, one client at a time. The Steam client ran first. The Epic Games client ran after it. Earlier notes covered four current-user `Run` values named `AF_uuid_*` / `AF_counter_*`.

> **🕵️ Executive summary**
>
> Both clients are Pawprint-signed Unity builds of Aniimo with FunPlus `FPX` libraries that reference AppsFlyer. They are not the same files. Steam app `4126040` is the newer tree and is the one tied to `AF_counter_4126040`. The Epic tree is older, uses `FPXEpic.dll` and the Epic Online Services SDK, and did not change that counter. `NEPKernel` was running during the Steam session and was stopped while the Epic process was the only `Aniimo.exe`. Neither snapshot showed a connection to `events.appsflyer.com`. Identifier `759396` is still separate and unexplained. Nothing in this evidence establishes that the game is malicious.

| | Steam investigation | Epic investigation |
| --- | --- | --- |
| When (+01) | 17:14:47–17:26 | 17:27:36–17:36 |
| Launcher parent | `steam.exe` | `EpicGamesLauncher.exe` |
| Catalogue id | Steam app `4126040` | Epic catalog item `cb669bb301ab4f6c97d24f98f41cc2e8` |
| Build | Updated 9 Oct 2026. Includes `FPXSteam.dll` | Files dated 23 Sep 2026. Includes `FPXEpic.dll`, no `FPXSteam.dll` |
| AppsFlyer strings | `events.appsflyer.com` paths under `/app/steam/` and `/app/nativepc/` | Same host under `/app/` in `FPXPluginCore.dll` |
| `AF_counter_4126040` | Read as `49` | Still `49`. No new `AF_*` name |
| `NEPKernel` | Running, image in the Steam tree | Stopped. Service entry still pointed at the Steam file |
| Extra peers | Alibaba ports `10000` / `20000` / `443`, one Tencent `443` | Those networks, plus Cloudflare and Amazon on `443` |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Overview</h2>

| Field | Value | Confidence |
| --- | --- | --- |
| Application in scope | Aniimo on Steam (`4126040`) and on the Epic Games launcher | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Separate installs, separate process parents |
| Steam session | 2026-10-11 17:14:47–17:26 +01 | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Creation time and a later existence check |
| Epic session | 2026-10-11 17:27:36–17:36 +01 | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Parent `EpicGamesLauncher.exe`; Steam `Aniimo.exe` already gone |
| Publisher (Steam) | Pawprint Studio | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Store `appdetails` and uninstall `Publisher` |
| Code signing subject | PAWPRINT INTERACTIVE ENTERTAINMENT PTE. LTD., Singapore | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Authenticode on the game binaries |
| SDK vendor in `FPX` version resources | FunPlus | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Version-resource text; Authenticode signer of those DLLs is still Pawprint |
| AppsFlyer-related code | Strings and URLs inside `FPX.dll` and `FPXSteam.dll` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> File strings, not a packet capture |
| Second identifier | `759396` | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Registry suffix only; Steam `appdetails` returned `success: false` |
| Malware determination | None made | Explicitly **not** claimed |

> **ℹ️ Note**
>
> Developing case study — **not** a completed security audit. Analytics code, a kernel driver, and third-party connections are trust and privacy findings. They do not, on their own, demonstrate malicious activity.

| Companion | Purpose |
| --- | --- |
| <a href="technical-overview.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Technical overview</a> | Processes, files, signatures, driver, registry |
| <a href="privacy-and-telemetry.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Privacy and telemetry</a> | AppsFlyer strings, endpoints, what traffic does not prove |
| <a href="investigation-log.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log</a> | **Authoritative** chronology and commands |

<h3 style="text-align:center">On this page</h3>

- [Technical sections](#technical-sections)
- [Security considerations](#security-considerations)
- [Diagnostic commands](#diagnostic-commands)
- [Findings and unresolved questions](#findings-and-unresolved-questions)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Technical sections</h2>

<h3 style="text-align:center">Developer and publisher</h3>

| Source | What it states |
| --- | --- |
| Steam `appdetails` for `4126040`, accessed 2026-10-11 | Name Aniimo, type `game`, developer and publisher Pawprint Studio, site `https://www.aniimo.com/`, release 15 Sep 2026 |
| Uninstall entry | `Publisher=Pawprint Studio`, `URLInfoAbout=https://www.aniimo.com/` |
| Authenticode on `Aniimo.exe`, `UnityPlayer.dll`, `GameAssembly.dll`, `FPX*.dll`, `NEP2.dll` | Subject `CN=PAWPRINT INTERACTIVE ENTERTAINMENT PTE. LTD.`, Singapore |

The certificate subject and the Steam name are both first-hand. This case does not add a corporate-registry filing that equates the studio name with the Pte. Ltd. entity. They are recorded as the two names the artefacts actually carry.

<h3 style="text-align:center">Official distribution channels</h3>

| Channel | Notes | Confidence |
| --- | --- | --- |
| Steam | App ID `4126040`; local manifest `buildid` `25828794`; uninstall via Steam | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Epic Games launcher | Separate install. Process parent was `EpicGamesLauncher.exe` at 17:27:36 +01. Catalog item id `cb669bb301ab4f6c97d24f98f41cc2e8` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local process and `.egstore` manifest metadata |

The Epic tree is a different build from the Steam tree (hashes differ for `Aniimo.exe`, `GameAssembly.dll`, and `FPX.dll`). It has `FPXEpic.dll` and `EOSSDK-Win64-Shipping.dll`, and it has no `FPXSteam.dll`. File dates on the Epic tree are 23 Sep 2026; the Steam tree was updated 9 Oct 2026. Install paths are not published.

<h3 style="text-align:center">Installation and update behaviour</h3>

The manifest `LastUpdated` value `1791574437` converts to 2026-10-09 20:33:57 +01, matching the timestamps on `Aniimo.exe` and `GameAssembly.dll`. `SizeOnDisk` was `31405161964` bytes. No install-time trace was captured. `repair.exe` is present and signed by Pawprint; it was not running in the process window that was listed.

<h3 style="text-align:center">Files and directories</h3>

Install location from the uninstall key, redacted here as `<STEAM_LIBRARY>\steamapps\common\Aniimo`. Unity layout: `Aniimo.exe`, `UnityPlayer.dll` file version `2022.3.62.13482230`, `GameAssembly.dll`, `UnityCrashHandler64.exe`, and `Aniimo_Data\`. `app.info` contains the two lines `Aniimo` and `Aniimo`.

Notable components, with the evidence label on the **attribution**, not on mere file presence (presence is confirmed):

| Component | Attribution supported by |
| --- | --- |
| `FPX.dll`, `FPXGame.dll`, `FPXSteam.dll` | Version resource company FunPlus; AppsFlyer strings in `FPX.dll` and `FPXSteam.dll` |
| `CrashSight64.dll` | Signed by FunPlus International AG; product CrashSight `2.2.9.1076` |
| `GMESDK.dll` | Signed by Tencent Technology (Shenzhen) Company Limited; product Tencent Cloud GME |
| `steam_api64.dll` | Signed by Valve Corp. |
| `discord_partner_sdk.dll` | Filename and Pawprint signature; load into the process was **not** verified |
| `NEP2.dll`, `NEPKernel.sys`, `NEPCleaner.exe` | NetEase strings; cleaner signed by NetEase (Hangzhou) Network Co., Ltd |

Detail: [technical overview](technical-overview.md).

<h3 style="text-align:center">Processes and services</h3>

Observed parent chain while the game was running:

```text
explorer.exe → steam.exe → Aniimo.exe → UnityCrashHandler64.exe
steam.exe → gameoverlayui64.exe
```

`gameoverlayui64.exe` is the Steam overlay (parent `steam.exe`), not a second game image. During the Steam session, `NEPKernel` was a running kernel driver, start mode Manual, image path inside the Steam install. By 17:34, after that process was gone and the Epic process was the only `Aniimo.exe`, the same service was **Stopped** and still pointed at the Steam copy of `NEPKernel.sys`. The Epic install contains its own `NEPKernel.sys` (same hash as the Steam file) and no `NEPCleaner.exe`. Module enumeration of the Steam `Aniimo.exe` returned access denied. The Epic image path was readable and matched the Epic install. `gamerun.lock` in that tree recorded the Epic process at 17:28:00.

<h3 style="text-align:center">Registry entries and startup behaviour</h3>

Located under:

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

| Value name | Public documentation | 2026-10-11 note |
| --- | --- | --- |
| `AF_uuid_4126040` | `[REDACTED GUID]` | Still GUID-shaped; not compared with the earlier redacted value, which was not retained |
| `AF_counter_4126040` | Earlier notes: `48`. Steam session: `49` | Still `49` during the later Epic session (17:34 and 17:36). The Epic launch did not add a new `AF_*` name |
| `AF_uuid_759396` | `[REDACTED GUID]` | Unchanged question |
| `AF_counter_759396` | Earlier notes: `0`. This session: `0` | This play session did not change it |

`StartupApproved\Run` stores 12-byte values for all four names. Each begins `03 00 00 00`. The following FILETIME decodes to 2026-09-27 13:23:18 UTC (the two suffixes differ by under a millisecond). Together with the earlier Startup Apps UI report, the disabled state is <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> **Strongly supported**. No `AF_*` names were found under the machine `Run` keys that were checked.

<h3 style="text-align:center">Network activity</h3>

Both clients had established TCP connections to public addresses on ports `10000`, `20000`, and `443`, plus a loopback pair to themselves. RIPE prefix overview (accessed 2026-10-11) places the game-port peers in Alibaba ASN `45102`. Port `443` peers include that Alibaba range, one Tencent address seen from both clients (`43.153.246.111`), and, for the Epic process only, Cloudflare `104.18.124.108` and Amazon `32.195.183.168`. None of the snapshot peers was an address returned for `events.appsflyer.com`. Payloads and TLS server names were not captured. Hosts embedded in the SDK are listed in [Privacy and telemetry](privacy-and-telemetry.md#endpoints).

See [Privacy and telemetry](privacy-and-telemetry.md).

<h3 style="text-align:center">Permissions and persistence</h3>

| Item | Status |
| --- | --- |
| `AF_*` scope | Current-user `Run` key; StartupApproved bytes match a disabled entry |
| `NEPKernel` | Service key present, type kernel driver, start Manual, state Running during the session |
| After exit | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> The game was still running at the last check |
| Scheduled tasks named for Aniimo, NEP, FunPlus, Pawprint, CrashSight, AppsFlyer | None matched |

`uninstall_cleaner.bat` is a shipped script that deletes CrashSight logs, a `wesight` directory, `gamerun.lock`, and `NEPKernel.sys`. It was read, not executed. It does not contain a service-delete command.

<h3 style="text-align:center">Privacy and telemetry</h3>

See [Privacy and telemetry](privacy-and-telemetry.md). AppsFlyer is no longer only a community name match: the endpoint host is inside the FunPlus SDK binaries. That still does not prove which fields left the PC during this session.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Security considerations</h2>

| Finding | Label |
| --- | --- |
| Defender threat list empty; no detection events (IDs 1006, 1015, 1116–1120) in the 180 days queried | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Absence in those queries — not proof of safety, and not a false-positive case, because no detection record was there to reclassify |
| Quick scan finished 2026-10-11 13:24:15 +01, before the Steam launch | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Event `1001`. The scan id is omitted |
| Real-time protection was on during the session | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `Get-MpComputerStatus` |
| Running Microsoft-signed kernel driver from the game directory | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Presence and signature. Purpose is a protector component, not a malware verdict |
| Aniimo is malware | **Not claimed** |
| Aniimo is definitively harmless | **Not claimed** |

See [Threat assessment](../../concepts/threat-assessment.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Diagnostic commands</h2>

Case-specific commands and observed results: [investigation log — Commands used](investigation-log.md#commands-used).

Reusable references:

- [Processes](../../commands/windows/processes.md)
- [Networking](../../commands/windows/networking.md)
- [Startup items](../../commands/windows/startup-items.md)
- [Registry](../../commands/windows/registry.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Findings and unresolved questions</h2>

<h3 style="text-align:center">Timeline</h3>

| When (+01) | Observation |
| --- | --- |
| Earlier notes | Four `AF_*` values; counter `48` / `0`; user-reported Defender quick scan with no threats; no binary or network inspection |
| 2026-09-27 13:23:18 UTC | StartupApproved FILETIME on all four `AF_*` names |
| 2026-10-09 20:33:57 | Manifest `LastUpdated`; game binaries share that update window |
| 2026-10-11 13:23:56–13:24:15 | Defender quick scan finished; no threat objects afterwards |
| 2026-10-11 17:14:47 | `Aniimo.exe` started, parent `steam.exe` |
| 2026-10-11 17:14:50 | `UnityCrashHandler64.exe` started, parent the game |
| 2026-10-11 17:15:18 | `gamerun.lock` recorded that PID |
| 2026-10-11 17:15:46–17:26 | Steam-client registry, files, sockets, Defender log, and signatures |
| 2026-10-11 17:27:36 | Epic-launcher `Aniimo.exe` started. Steam `Aniimo.exe` was no longer running |
| 2026-10-11 17:34–17:36 | Epic install compared. `AF_counter_4126040` still `49`. `NEPKernel` Stopped |

<h3 style="text-align:center">Established observations</h3>

- The running game process is the child of `steam.exe`, and the install lock file names that PID.
- Steam’s public app record and the local manifest both identify `4126040` as Aniimo by Pawprint Studio.
- `FPX.dll` and `FPXSteam.dll` contain AppsFlyer URLs and the `AF_uuid` / `AF_counter` names next to the `Run` key path.
- `AF_counter_4126040` is now `49`; `AF_counter_759396` is still `0`.
- `NEPKernel` was running from the install directory during play.
- Connection snapshots did not include the AppsFlyer addresses resolved in the same session.
- Defender’s queried detection events and threat list did not contain a detection to associate with Aniimo.

<h3 style="text-align:center">❓ Unresolved questions</h3>

- Which process wrote each `AF_*` value? The SDK strings make the FunPlus component the leading explanation; there is no write trace.
- Did an AppsFlyer or CrashSight upload happen outside the two TCP snapshots?
- What is `759396`? It was not in the searched binaries, not in the checked Steam manifests, and Steam `appdetails` returned `success: false`.
- Does `NEPKernel` stay running after the game exits?
- Which personal-data fields, if any, were populated in an outbound request? Encrypted or uncaptured payloads cannot answer that.

<h3 style="text-align:center">Final assessment</h3>

> **❓ Unverified as a safety verdict**
>
> Available evidence does **not** establish that Aniimo is malware. It **does** establish a signed Unity game with a FunPlus SDK that references AppsFlyer, a NetEase protector driver loaded at runtime, and ordinary-looking cloud gameplay connections whose payloads were not read. The investigation remains incomplete where writer identity, `759396`, and post-exit behaviour are concerned.

Detail: [investigation log — conclusions](investigation-log.md#interpretation-and-current-conclusions).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Commands and sanitised results in the [investigation log](investigation-log.md), session 2026-10-11
- Steam store `appdetails` for `4126040`, accessed 2026-10-11; the same endpoint for `759396` returned `success: false`
- RIPE Stat prefix overview for the observed peer addresses, accessed 2026-10-11
- [FunPlus User Privacy Policy](https://funplus.com/user-privacy-policy/), last updated 23 December 2021, accessed 2026-10-11 — general controller/processor disclosure, not a capture of this session
- Earlier user observations preserved in the investigation log (Startup Apps UI, counter `48`, undated quick-scan report)
- [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [Investigation log](investigation-log.md)
- [Evidence and confidence](../../methodology/evidence-and-confidence.md)
