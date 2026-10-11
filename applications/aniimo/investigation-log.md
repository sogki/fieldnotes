<h1 style="text-align:center">🕵️ Aniimo — investigation log</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Aniimo](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Authoritative chronology for the developing Aniimo / `AF_*` startup registry case study. Other Aniimo pages summarise findings and link here for command detail.

> **ℹ️ Publication note**
>
> Personal identifiers are redacted. GUID values appear as `[REDACTED GUID]`. Steam library paths use placeholders. Unrelated startup applications are omitted.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Summary</h2>

| | |
| --- | --- |
| **Status** | Hands-on case study — live session 2026-10-11 while the game was running. Not a security audit |
| **Focus** | Two running clients: Steam (17:14) and Epic Games launcher (17:27). Install trees, `AF_*` values, AppsFlyer endpoints, network metadata, Defender records |
| **Strongest link** | Steam `4126040` is Aniimo. `FPX.dll` / `FPXSteam.dll` contain `AF_uuid`, `AF_counter`, the `Run` key path, and `events.appsflyer.com` |
| **Still open** | Writer of the registry values; whether an analytics upload occurred outside the snapshots; meaning of `759396`; driver state after exit |
| **Assessment** | Evidence does **not** establish malware. It does establish the SDK strings, a running NetEase-identified driver, and cloud connections whose payloads were not read |

<h3 style="text-align:center">Established observations</h3>

- Four `AF_*` values remain under the current user’s `Run` key. Counters on 2026-10-11: `49` and `0` (earlier notes recorded `48` and `0`).
- StartupApproved blobs for those names begin with `03 00 00 00`; FILETIME 2026-09-27 13:23:18 UTC.
- `Aniimo.exe` started at 17:14:47 +01 as a child of `steam.exe`. `gamerun.lock` in the install recorded that PID.
- Steam `appdetails`, the local manifest, and the uninstall entry identify Pawprint Studio’s Aniimo, app `4126040`.
- FunPlus-identified `FPX` binaries contain the AppsFlyer event URLs and the `AF_*` names. No TCP connection to the resolved AppsFlyer addresses was present in the checks below.
- `NEPKernel` was Running during the Steam session, start mode Manual, image in the Steam install, Microsoft-signed. At 17:34, with only the Epic process running, that service was Stopped and still pointed at the Steam file.
- Defender threat list was empty; detection event IDs over 180 days returned no events. A quick scan had finished earlier the same day, before launch. The scan id is omitted.
- The Epic launch at 17:27 used a separate install and did not change `AF_counter_4126040` (`49`) or add an `AF_*` name. Endpoint list: [privacy and telemetry](privacy-and-telemetry.md#endpoints).

<h3 style="text-align:center">❓ Unresolved questions</h3>

- Which process wrote each registry value?
- Did AppsFlyer or CrashSight traffic occur outside the two TCP snapshots?
- What is `759396`?
- Does `NEPKernel` remain running after the game exits?
- Which template fields, if any, were actually sent?

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Chronology](#chronology)
- [Commands used](#commands-used)
- [Interpretation and current conclusions](#interpretation-and-current-conclusions)
- [Follow-up research](#follow-up-research)
- [Explicit non-claims](#explicit-non-claims)
- [Related resources](#related-resources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Chronology</h2>

<h3 style="text-align:center">1. Unfamiliar Startup Apps entries</h3>

**Question:** What are these Startup Apps entries, and do they look like normal autostart commands?

**Source:** User observation (Task Manager / Startup Apps UI)

**Observed:** Four unfamiliar entries, reported as **disabled**, with no identifiable publisher and no ordinary executable path shown in that UI.

**Supports:** Something registered unusual startup-related items at the user level.

**Still unknown:** Writer process, purpose, and whether the UI “disabled” state removed or only suppressed the entries.

**Next step:** Locate the underlying registry (or other) persistence location.

> **ℹ️ Note**
>
> Unfamiliar registry or startup entries do not, on their own, demonstrate malicious activity.

<h3 style="text-align:center">2. Location in the current-user `Run` key</h3>

**Question:** Where are the entries stored?

**Source:** Read-only registry inspection (see [commands B–C](#b-inspect-the-four-specific-run-values))

**Observed:** Values under:

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

| Value name | Sanitised observed data |
| --- | --- |
| `AF_uuid_4126040` | `[REDACTED GUID]` |
| `AF_counter_4126040` | `48` |
| `AF_uuid_759396` | `[REDACTED GUID]` |
| `AF_counter_759396` | `0` |

**Supports:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Presence and stored forms of the four values (GUIDs redacted for publication).

**Still unknown:** Who wrote them; whether Windows treats the data as launch strings.

<h3 style="text-align:center">3. Association with Aniimo via Steam app ID</h3>

**Question:** Does suffix `4126040` correspond to a known installed application?

**Source:** Steam app ID match + user confirmation + library metadata (see [command F](#f-inspect-steam-library-configuration))

**Observed:**

- Suffix `4126040` matches Aniimo’s Steam application ID.
- The user confirmed installing and playing Aniimo through Steam.
- Inspected Steam library configuration included application ID `4126040` (Aniimo).

**Supports:** <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Aniimo was present in the Steam environment under study.

**Still unknown:** Causal proof that Aniimo (or a shipped component) wrote the registry values.

<h3 style="text-align:center">4. Second identifier `759396`</h3>

**Question:** What application or catalogue ID is `759396`?

**Source:** Steam manifest search + library metadata (see [commands E–F](#e-check-installed-steam-game-manifests-for-759396))

**Observed:**

- Parallel `AF_*_759396` pair present.
- No matching `appmanifest_*.acf` for `759396` in the specific libraries checked.
- `759396` absent from the reported installed-app list in reviewed `libraryfolders.vdf` metadata.

**Supports:** Negative result for those checked locations only.

**Still unknown:** Origin of `759396` (uninstalled game, other library, or non-Steam software remain possible) — <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown**.

<h3 style="text-align:center">5. Community AppsFlyer association</h3>

**Question:** Do similar names appear in third-party discussions of analytics SDKs?

**Source:** Community reports (third-party claim)

**Observed:** Similar `AF_uuid_[APPID]` / `AF_counter_[APPID]` naming associated with AppsFlyer analytics / attribution in community reports.

**Supports:** <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** research lead only, as of this section.

**Still unknown at the time:** Whether AppsFlyer (or any SDK) was present in *this* installation.

**Later (2026-10-11):** [Section 8](#8-live-session-2026-10-11) found those strings and URLs in `FPX.dll` and `FPXSteam.dll`. This section is left as the original lead. It is not rewritten into a confirmation.

<h3 style="text-align:center">6. Microsoft Defender Quick scan</h3>

**Question:** Did a quick local malware scan report threats?

**Source:** User-reported Microsoft Defender Quick scan

**Observed:** No threats reported.

**Supports:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> That specific scan result at that time.

**Still unknown:** Broader security posture; Quick scan is not a full forensic analysis.

<h3 style="text-align:center">7. Work not performed before 2026-10-11</h3>

As of the original write-up, the following had **not** been performed:

- Executable / binary analysis
- Packet capture or network inspection
- Independent forensic analysis beyond commands A–G

[Section 8](#8-live-session-2026-10-11) is the later live session. It does not include a packet capture. Payload contents remain unexamined.

<h3 style="text-align:center">8. Live session 2026-10-11</h3>

**Question:** What does this install do while Aniimo is running, and which earlier leads can be checked without changing the game?

**Source:** Read-only process, file, registry, socket, signature, and Defender queries. Timestamps below are local (UTC+1) unless noted. Personal paths, the LAN address, GUIDs, and account-like PlayerPrefs suffixes are redacted. The game was still running at 17:26:10.

**Observed — process**

| Time | Relationship |
| --- | --- |
| 17:10:18 | `explorer.exe` |
| 17:12:54 | `steam.exe`, parent `explorer.exe` |
| 17:14:47 | `Aniimo.exe`, parent `steam.exe` |
| 17:14:50 | `UnityCrashHandler64.exe`, parent `Aniimo.exe` |
| 17:15:07 | `gameoverlayui64.exe`, parent `steam.exe` (Steam overlay) |
| 17:15:18 | `<STEAM_LIBRARY>\steamapps\common\Aniimo\gamerun.lock` text included `pid=` the Aniimo PID and `tstr=2026-10-11_17:15:18.473` |

CIM `ExecutablePath` / `CommandLine` for the game were empty. A later module listing returned “Access is denied.” The lock file is the direct tie between that PID and this install tree.

**Observed — identity and files**

- Uninstall entry: display name Aniimo, publisher Pawprint Studio, `URLInfoAbout=https://www.aniimo.com/`, uninstall `steam://uninstall/4126040`, install location the Aniimo directory above.
- Manifest `appmanifest_4126040.acf`: name Aniimo, `buildid` `25828794`, `SizeOnDisk` `31405161964`, `LastUpdated` `1791574437` (2026-10-09 20:33:57 +01). No `appmanifest_759396.acf` in the Steam install library or the additional library.
- Steam `appdetails?appids=4126040` (`success: true`): developer and publisher Pawprint Studio, release 15 Sep 2026, website `https://www.aniimo.com/`. `appdetails?appids=759396` returned `success: false`.
- Authenticode Valid on the binaries listed in [command J](#j-authenticode-and-version-resources). Pawprint signs the game and the `FPX` DLLs. FunPlus International AG signs `CrashSight64.dll`. Valve signs `steam_api64.dll`. Tencent signs `GMESDK.dll`. NetEase (Hangzhou) Network Co., Ltd signs `NEPCleaner.exe`. Microsoft Windows Hardware Compatibility Publisher signs `NEPKernel.sys`.
- `findstr` hits: `AppsFlyer`, `appsflyer`, `AF_uuid`, and `AF_counter` in `FPX.dll` and/or `FPXSteam.dll`. No hits for those needles in `FPXGame.dll`, `GameAssembly.dll`, `NEP2.dll`, or `Aniimo.exe`. No literal `4126040` or `759396` in the targeted set. No `onelink.me`.
- URL strings in the `FPX` DLLs include the six `events.appsflyer.com` paths recorded in [privacy and telemetry](privacy-and-telemetry.md), plus FunPlus, Steam partner API, and Discord API URLs. Context around `AF_uuid` includes the `Run` key path and `Error opening key` / `Error setting key`.
- `NEPCleaner.exe` version resource describes NetEase Protector Cleanup Utility. The file contains `NetEase`, `Protector` (also in `NEP2.dll`), and a PDB path whose directories include `anti-cheat\nep-search\NEPUninstaller`. The cleaner was not running.
- `NEPKernel` service: Running, Manual, kernel driver, image path the `NEPKernel.sys` in this install. `NetEase` is a string in the `.sys`.
- `uninstall_cleaner.bat` (read, not run) deletes CrashSight directories, `CrashSightLog`, `PVideoPlayerCache`, `gamerun.lock`, and `NEPKernel.sys`. It has no `sc delete`.

**Observed — registry during play**

- `AF_counter_4126040` = `49` at 17:15:46 and at 17:22. `AF_counter_759396` = `0` both times. UUID values remained GUID-shaped and are not republished.
- StartupApproved 12-byte values, first byte `0x03`, FILETIME 2026-09-27 13:23:18 UTC for all four names (sub-millisecond difference between the two suffixes).
- `HKCU\Software\Aniimo\Aniimo` value **names** include `BI_AppFirstLaunchSent`, `FPC_AF_REG_LAST_ACCOUNT_V1_<redacted>`, and `FPC_AD_FIRST_OPEN_REPORTED_<redacted>`. Reading the key with `Get-ItemProperty` failed with a cast error, so the data was not recorded.
- `HKCU\Software\funplus.sdk` exists (value name `orginuuid`; data not read). `%ProgramData%\funpluspcsdk\PCSDK\worldx.global.prod\` had a cache file written at 17:15:51.
- `%LOCALAPPDATA%\appsflyer` contains two files (48 bytes and 16 bytes) last written 2024-12-25 and 2024-12-31. Their ASCII did not contain either current `AF_uuid` string.

**Observed — network**

Two `Get-NetTCPConnection` snapshots for the game PID, at 17:17:54 and 17:20:58. Peers and RIPE ASN holders are tabulated in the [technical overview](technical-overview.md#network-activity). Loopback `59083`/`59084` was the process connected to itself. No PTR records. DNS cache had no entries matching `appsflyer`, `crashsight`, `funplus`, `aniimo`, `pawprint`, or `netease`.

`events.appsflyer.com` resolved to `3.174.193.38`, `.121`, `.122`, and `.82`. Connections to those addresses: none. The CrashSight log line at 17:15:16 names `pc.crashsight.wetest.net` (resolved later to `43.163.62.120`, not a snapshot peer). An app token on that log line is redacted.

**Observed — Defender**

| Query | Result |
| --- | --- |
| `Get-MpComputerStatus` at 17:17:57 | Real-time protection on. Quick scan 13:23:56–13:24:15 +01. Signatures last updated 05:28:13 +01, then a later engine update was visible in the event log around 17:20 |
| `Get-MpThreat` / `Get-MpThreatDetection` | Count 0 |
| Operational events 1006, 1015, 1116, 1117, 1118, 1119, 1120 since 180 days before 17:19 | “No events were found that match the specified selection criteria.” |
| Event 1001 at 13:24:15 | Quick scan finished. Duration 19 seconds. This is before the 17:14 Steam launch. The scan id is omitted |
| Event 2010 at 17:14:54 | Cloud security-intelligence update. Message does not name Aniimo. The same event ID also occurred at 13:14, 14:34, 16:37, 17:20, and 17:21 |
| Exclusion path/process names matching Aniimo, NEP, FunPlus, CrashSight, Pawprint | None |

There is no detection name, affected object, or remediation action to record. The earlier undated “quick scan reported no threats” note remains a separate user report. Today’s event 1001 is the timestamped scan. Event 2010 is not a detection and is not attributed to the game.

**Supports:** File-level AppsFlyer integration in the FunPlus SDK; process parentage; driver loaded during play; Defender has no detection record in the queries above; `759396` was not explained.

**Does not establish:** The writer of the registry values; the contents of any connection; that analytics was or was not sent before the first snapshot; post-exit persistence; a malware or false-positive verdict.

**Limitations:** No packet capture, no SNI, no module list, no pre-launch counter read. `pc-client-callback.funplus.com` resolved locally to `127.0.0.1`, the hosts file had no matching entry, and a query to `1.1.1.1` timed out, so that answer is not treated as public DNS.

<h3 style="text-align:center">9. Epic Games launcher session, 2026-10-11 17:27</h3>

**Question:** Does a second Aniimo process, started from the Epic Games launcher, share the Steam install, the `AF_*` counters, the driver, and the same endpoints?

**Source:** Read-only process, hash, signature, string, socket, and registry checks from 17:34 to 17:36 +01. Install paths are omitted. Catalog ids below come from the Epic `.egstore` manifest metadata and identify the product, not an account.

**Observed**

- The only `Aniimo.exe` was a child of `EpicGamesLauncher.exe`, started 17:27:36. Its image path was readable and was not the Steam install. `UnityCrashHandler64.exe` was its child at 17:27:38. Epic overlay and web-helper processes were children of the launcher, not of the game.
- `gamerun.lock` in the Epic tree recorded that process at 17:28:00.917.
- `.egstore` catalog item `cb669bb301ab4f6c97d24f98f41cc2e8`, namespace `60a36a8a594c49ab9fb58fa33be6156e`, app name `13ac50a009be47a7a6de50354c19ff5b`.
- SHA-256 differs from the Steam copies of `Aniimo.exe`, `UnityPlayer.dll`, `GameAssembly.dll`, `NEP2.dll`, `FPX.dll`, and `FPXGame.dll`. `NEPKernel.sys` and `CrashSight64.dll` are identical. Epic has `FPXEpic.dll`, `FPXPluginCore.dll`, and Epic-signed `EOSSDK-Win64-Shipping.dll`. It has no `FPXSteam.dll` and no `NEPCleaner.exe`.
- Signatures: Pawprint on `Aniimo.exe`, `FPX.dll`, `FPXEpic.dll`, `FPXPluginCore.dll`, `FPXGame.dll`, and `NEP2.dll`. FunPlus version resources on the FPX files. `EOSSDK-Win64-Shipping.dll` signed by Epic Games Inc.
- Strings: `events.appsflyer.com`, `AF_uuid`, and `AF_counter` in Epic `FPX.dll` and `FPXPluginCore.dll`. The word `AppsFlyer` is also in `FPXEpic.dll`, which yielded no `https://` URL. No literal `4126040` or `759396`.
- `AF_counter_4126040` still `49`. `AF_counter_759396` still `0`. No additional `AF_*` name.
- `NEPKernel` was Stopped. The service image path was still the Steam copy of `NEPKernel.sys`.
- TCP peers at 17:34 are in the [endpoint table](privacy-and-telemetry.md#observed-tcp-peers). New relative to Steam: Cloudflare `104.18.124.108:443` and Amazon `32.195.183.168:443`. The Tencent address `43.153.246.111:443` appeared again. CrashSight log for this process names `pc.crashsight.wetest.net`.

**Supports:** Two installs, two builds, one publisher signature. The Epic launch did not move the Steam-suffixed counter. The kernel service was not running while the Epic process was.

**Does not establish:** That the Epic build never loads `NEPKernel`, or that Cloudflare and Amazon addresses are Epic’s own services rather than a CDN in front of someone else. No SNI was captured.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Commands used</h2>

Reusable patterns: [startup items](../../commands/windows/startup-items.md) · [registry](../../commands/windows/registry.md).

All commands below are <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only** unless noted.

<h3 style="text-align:center">A. Enumerate Windows startup commands</h3>

**Question:** What startup commands does Windows expose via CIM?

```powershell
Get-CimInstance Win32_StartupCommand |
    Select-Object Name, Command, Location, User |
    Format-List
```

| | |
| --- | --- |
| **Observed** | Four entries used `AF_uuid_` / `AF_counter_` naming. Data were GUID-style identifiers and numeric counters, not ordinary executable paths |
| **Supports** | Unusual startup-visible entries exist |
| **Limitation** | Incomplete persistence inventory; no creator identification |

<h3 style="text-align:center">B. Inspect the four specific `Run` values</h3>

**Question:** What exact data is stored for the four names?

```powershell
$path = 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run'

Get-ItemProperty -Path $path |
    Select-Object AF_uuid_4126040, AF_counter_4126040,
                  AF_uuid_759396, AF_counter_759396 |
    Format-List
```

| | |
| --- | --- |
| **Observed** | Sanitised values in [section 2](#2-location-in-the-current-user-run-key) |
| **Supports** | Current value contents |
| **Limitation** | No history; no writer process |

<h3 style="text-align:center">C. Inspect all values in the startup registry key</h3>

**Question:** How do the `AF_` values sit among other `Run` entries?

```powershell
Get-ItemProperty `
    'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' |
    Format-List *
```

| | |
| --- | --- |
| **Observed** | Four `AF_` values appeared alongside recognisable applications. Unrelated entries and personal paths omitted from this public write-up |
| **Supports** | Coexistence with ordinary autostart entries |
| **Limitation** | Presence ≠ malice; these four did not resemble ordinary executable commands |

<details>
<summary>Optional: why full key dumps are not published</summary>

Publishing a complete `Run` key dump can expose personal install paths and unrelated application inventory. Fieldnotes keeps only the four investigated values and high-level notes about neighbouring recognisable entries.

</details>

<h3 style="text-align:center">D. Filter startup commands to the unfamiliar entries</h3>

**Question:** Can the four entries be isolated cleanly?

```powershell
Get-CimInstance Win32_StartupCommand |
    Where-Object { $_.Name -like 'AF_*' } |
    Format-List Name, Command, Location, User
```

| | |
| --- | --- |
| **Observed** | All four under the current user’s `Run` location; GUID-style values and counters `48` / `0` |
| **Limitation** | Same coverage limits as command A |

<h3 style="text-align:center">E. Check installed Steam game manifests for `759396`</h3>

**Question:** Is `759396` present as an installed Steam app in checked libraries?

> **ℹ️ Note**
>
> Replace `<STEAM_INSTALL_ROOT>` and `<ADDITIONAL_STEAM_LIBRARY>` with valid paths before running.

```powershell
$steamRoots = @(
    '<STEAM_INSTALL_ROOT>',
    '<ADDITIONAL_STEAM_LIBRARY>'
)

foreach ($root in $steamRoots) {
    $steamApps = Join-Path $root 'steamapps'

    if (Test-Path $steamApps) {
        Get-ChildItem $steamApps -Filter 'appmanifest_*.acf' `
            -ErrorAction SilentlyContinue |
            ForEach-Object {
                $file = Get-Content $_.FullName -Raw

                if ($file -match '"appid"\s+"759396"') {
                    Write-Output "Matching manifest found"
                }
            }
    }
}
```

| | |
| --- | --- |
| **Observed** | No matching manifest from the specific libraries checked |
| **Limitation** | Does not rule out uninstalled games, other libraries, or non-Steam apps |

<h3 style="text-align:center">F. Inspect Steam library configuration</h3>

**Question:** Which app IDs appear in Steam’s reported installed-app metadata?

Steam’s `libraryfolders.vdf` was inspected. Actual drive letters, library paths, content identifiers, and unrelated inventory are **not** published.

| | |
| --- | --- |
| **Observed** | `4126040` (Aniimo) present; `759396` absent from the reported installed-app list |
| **Limitation** | Absence ≠ malice; absence ≠ “never installed” |

<h3 style="text-align:center">G. Attempt to inspect the registry key’s last-write time</h3>

**Question:** When was the `Run` key last modified?

```powershell
Get-Item 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' |
    Select-Object Name, LastWriteTime
```

| | |
| --- | --- |
| **Observed** | Key name returned; `LastWriteTime` **not** populated |
| **Conclusion** | No usable creation/modification timestamp from this command. None is invented here |

<h3 style="text-align:center">H. Re-read the four `Run` values and StartupApproved blobs</h3>

**Question:** Have the counters changed, and what do the StartupApproved bytes store?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' |
    Select-Object AF_uuid_4126040, AF_counter_4126040, AF_uuid_759396, AF_counter_759396

$ap = Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\StartupApproved\Run'
# 12-byte values; byte 0 recorded; bytes 4-11 decoded with [DateTime]::FromFileTimeUtc
```

| | |
| --- | --- |
| **Observed** | Counters `49` and `0`. UUID fields GUID-shaped (not published). StartupApproved first byte `0x03`; FILETIME 2026-09-27 13:23:18 UTC |
| **Supports** | Current data, and that the `759396` counter did not move during this session |
| **Limitation** | No pre-launch sample, so the change from the earlier `48` is not pinned to 17:14:47 |

<h3 style="text-align:center">I. Process tree and install location</h3>

**Question:** Which processes belong to the running game?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
Get-CimInstance Win32_Process |
    Where-Object { $_.CreationDate -ge (Get-Date '2026-10-11 17:12:00') } |
    Select-Object ProcessId, ParentProcessId, Name, CreationDate

Get-ItemProperty 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*',
                 'HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*',
                 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*' |
    Where-Object { $_.DisplayName -eq 'Aniimo' }
```

| | |
| --- | --- |
| **Observed** | Parent chain in [section 8](#8-live-session-2026-10-11). Uninstall publisher Pawprint Studio. `gamerun.lock` named the game PID |
| **Limitation** | `ExecutablePath` on the game process was empty; module enumeration was access denied |

<h3 style="text-align:center">J. Authenticode and version resources</h3>

**Question:** Who signed the game and the SDK binaries?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
Get-AuthenticodeSignature -FilePath <selected exe, dll, and NEPKernel.sys>
[Diagnostics.FileVersionInfo]::GetVersionInfo(<same files>)
```

| | |
| --- | --- |
| **Observed** | All selected files `Status=Valid`. Subjects and version-resource companies are in [section 8](#8-live-session-2026-10-11) |
| **Limitation** | A valid signature identifies the signer. It does not describe runtime behaviour |

<h3 style="text-align:center">K. String search for AppsFlyer, app ids, and NetEase</h3>

**Question:** Are the `AF_*` names and AppsFlyer hosts present in the install, or only in community reports?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
findstr /M /C:"AppsFlyer" /C:"AF_uuid" /C:"AF_counter" /C:"759396" /C:"4126040" <targeted exe and dll>
# ASCII windows around hits were then extracted in memory; dev keys were not copied out
```

| | |
| --- | --- |
| **Observed** | Hits in `FPX.dll` and `FPXSteam.dll` for the AppsFlyer host, `AF_uuid`, `AF_counter`, and the `Run` key path. No `759396` or `4126040` literal in that set. `NetEase` in `NEP2.dll`, `NEPKernel.sys`, and `NEPCleaner.exe` |
| **Limitation** | Strings are not a trace of a write or of a socket. The search was a targeted file list, not every byte of the 31 GB install |

<h3 style="text-align:center">L. TCP and UDP for the game PID</h3>

**Question:** Where is `Aniimo.exe` connected, and can those peers be named?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
Get-NetTCPConnection -OwningProcess <Aniimo PID>
Get-NetUDPEndpoint -OwningProcess <Aniimo PID>
Resolve-DnsName -Name <peer> -Type PTR
curl.exe -sS "https://stat.ripe.net/data/prefix-overview/data.json?resource=<peer>"
```

| | |
| --- | --- |
| **Observed** | Tables in the [technical overview](technical-overview.md#network-activity). No PTR. RIPE holders Alibaba ASN `45102` or, for `43.153.246.111`, Tencent ASN `132203` |
| **Limitation** | Point-in-time. No payload, no SNI. UDP had a local bind only |

<h3 style="text-align:center">M. Compare AppsFlyer and CrashSight names with live sockets</h3>

**Question:** Do the SDK hostnames appear as connections?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
Resolve-DnsName events.appsflyer.com -Type A
Resolve-DnsName pc.crashsight.wetest.net -Type A
Get-DnsClientCache | Where-Object { $_.Entry -match 'appsflyer|crashsight|funplus|aniimo' }
Get-NetTCPConnection | Where-Object { $_.RemoteAddress -in <AppsFlyer answers> }
```

| | |
| --- | --- |
| **Observed** | AppsFlyer answers `3.174.193.38/82/121/122`; zero matching TCP connections. CrashSight log names `pc.crashsight.wetest.net`; its answer `43.163.62.120` was not a snapshot peer. DNS-cache filter was empty |
| **Limitation** | Does not prove those hosts were never contacted |

<h3 style="text-align:center">N. Defender status, threat list, and operational events</h3>

**Question:** What did Defender actually record, and does any record name Aniimo?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**. No exclusions were added and real-time protection was not changed.

```powershell
Get-MpComputerStatus
Get-MpThreat
Get-MpThreatDetection
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-Windows Defender/Operational'
    Id = 1006,1015,1116,1117,1118,1119,1120
    StartTime = (Get-Date).AddDays(-180)
}
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-Windows Defender/Operational'
    Id = 1001,2010
    StartTime = (Get-Date).AddDays(-2)
}
```

| | |
| --- | --- |
| **Observed** | Results in [section 8](#8-live-session-2026-10-11). No detection object and no detection event |
| **Limitation** | A cleared log, or an event outside the 180-day filter, would not appear. Event 2010’s text was read far enough to see that it does not name the game |

<h3 style="text-align:center">O. Steam catalogue check for both ids</h3>

**Question:** Does public Steam metadata identify `4126040` and `759396`?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
curl.exe -sS "https://store.steampowered.com/api/appdetails?appids=4126040&l=english"
curl.exe -sS "https://store.steampowered.com/api/appdetails?appids=759396&l=english"
```

| | |
| --- | --- |
| **Observed** | `4126040` success, Pawprint Studio, Aniimo. `759396` `success: false` |
| **Limitation** | A false `success` flag does not prove the number was never a Steam id (unlisted or removed apps can fail this endpoint) |

<h3 style="text-align:center">P. Driver service key and scheduled tasks</h3>

**Question:** Is the protector only a file, or a loaded driver, and is there a matching task?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
Get-CimInstance Win32_SystemDriver -Filter "Name='NEPKernel'"
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Services\NEPKernel'
Get-ScheduledTask | Where-Object { $_.TaskName -match 'Aniimo|NEP|FunPlus|Pawprint|CrashSight|AppsFlyer' }
```

| | |
| --- | --- |
| **Observed** | Driver Running / Manual / image under the install. No matching scheduled task |
| **Limitation** | Service-key `LastWriteTime` was not obtained (`Get-Item` returned no object). A later check at 17:34, after the Steam process had exited, found the service Stopped. See [section 9](#9-epic-games-launcher-session-2026-10-11-1727) |

<h3 style="text-align:center">Q. Epic install comparison</h3>

**Question:** Is the Epic-launched process the same files, the same `AF_*` state, and the same peers?

<img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**

```powershell
Get-CimInstance Win32_Process | Where-Object { $_.Name -match 'Aniimo|EpicGamesLauncher' }
Get-FileHash <paired binaries> -Algorithm SHA256
Get-AuthenticodeSignature <Epic Aniimo.exe, FPX.dll, FPXEpic.dll, FPXPluginCore.dll, EOSSDK-Win64-Shipping.dll>
Get-NetTCPConnection -OwningProcess <Epic Aniimo PID>
Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run'
Get-CimInstance Win32_SystemDriver -Filter "Name='NEPKernel'"
```

| | |
| --- | --- |
| **Observed** | [Section 9](#9-epic-games-launcher-session-2026-10-11-1727). Hashes, catalog ids, peers, and the stopped driver |
| **Limitation** | No SNI. `fpx_config_pc` was not decrypted. Catalog ids are product metadata from the local manifest |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Interpretation and current conclusions</h2>

<h3 style="text-align:center">Established observations</h3>

Listed in the [Summary](#summary) above.

<h3 style="text-align:center">Interpretation after the live session</h3>

| Claim | Label |
| --- | --- |
| `4126040` is Aniimo by Pawprint Studio on Steam | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Store API, manifest, uninstall entry |
| The 2026-10-11 `Aniimo.exe` is that install | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Parent `steam.exe`, image name, and `gamerun.lock`. Process image path was not readable |
| `FPX` / `FPXSteam` contain an AppsFlyer client aimed at the `Run` key and `events.appsflyer.com` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Strings |
| That code wrote the observed `AF_*` values | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Name match and co-located registry path. No write trace |
| An AppsFlyer request left the machine during the snapshots | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |
| `759396` is a second Steam app installed here | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |
| `NEPKernel` is a NetEase protector driver loaded for this game | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Same directory, NetEase strings, NetEase-signed cleaner, Microsoft-signed `.sys` running. The process that started it was not observed |
| Defender detected Aniimo | **Not supported.** No detection record in the queries |
| The game is malware, or is proven harmless | **Not claimed** |

Unfamiliar registry entries, an analytics SDK, and a kernel driver do not, on their own, demonstrate malicious activity. See [Telemetry and analytics](../../concepts/telemetry-and-analytics.md).

<h3 style="text-align:center">Final assessment</h3>

The available evidence does **not** establish that Aniimo is malware. It establishes a Pawprint-signed Unity game whose FunPlus SDK binaries reference AppsFlyer and the `AF_*` registry names, a NetEase-identified driver that was running during play, and outbound connections to Alibaba-held and one Tencent-held prefix. It does not establish the registry writer, the body of any connection, or the meaning of `759396`. The investigation remains incomplete on those points.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Follow-up research</h2>

Done on 2026-10-11, with the limits recorded in [section 8](#8-live-session-2026-10-11):

- [x] Examine install files and Authenticode signatures
- [x] Read Defender threat state and recent operational events
- [x] Observe TCP metadata while the game was running
- [x] Compare AppsFlyer hostnames with those sockets
- [x] Read the FunPlus privacy policy as a vendor document, not as a capture

Still open, in priority order:

1. Sample `NEPKernel` state after Aniimo exits, without stopping the driver by hand.
2. On a later launch, read `AF_counter_4126040` immediately before and after start. A move from `49` to `50` would tie the counter to a launch more tightly than this session could.
3. Optional metadata-only DNS or SNI capture at startup for `events.appsflyer.com` and `pc.crashsight.wetest.net`. Do not publish payloads or identifiers.
4. Keep looking for a primary catalogue entry for `759396`. Steam `appdetails` and the store URL fetch did not provide one. Do not adopt an uncorroborated third-party label.
5. Re-read the non-secret PlayerPrefs scalars with a method that does not cast-fail the whole key. Leave account suffixes redacted.
6. A game-specific privacy policy was not in the Aniimo page fetched here. Check again if Pawprint publishes one.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Explicit non-claims</h2>

- Aniimo is **not** labelled malware.
- Aniimo is **not** certified harmless.
- AppsFlyer **strings and URLs** are verified in `FPX.dll` and `FPXSteam.dll`. An AppsFlyer **upload during this session** is **not** verified.
- Template field names (`steamid`, `ip`, device fields) are **not** proof those values were sent.
- A clean Defender result is **not** a completed security audit, and it is **not** a false-positive finding: no detection record was present to reclassify.
- Event `2010` at launch time is **not** a detection of Aniimo.
- Negative Steam checks for `759396` are **not** conclusive exclusion of every possible origin.
- The running kernel driver is **not** treated as malicious merely because it is kernel code.
- `uninstall_cleaner.bat` was **not** executed. Defender exclusions were **not** added. The install was **not** modified.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Aniimo overview](README.md)
- [Technical overview](technical-overview.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [Startup items](../../commands/windows/startup-items.md)
- [Registry](../../commands/windows/registry.md)
- [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)
- [Evidence and confidence](../../methodology/evidence-and-confidence.md)
