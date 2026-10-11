<h1 style="text-align:center">Aniimo — technical overview</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Aniimo](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Technical context for the [Aniimo case study](README.md). Command transcripts and sanitised results live only in the [investigation log](investigation-log.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">🔎 Application identification</h2>

| Item | Detail | Confidence |
| --- | --- | --- |
| Steam application ID | `4126040` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local manifest, uninstall entry, and Steam `appdetails` |
| Catalogue name | Aniimo | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Same sources |
| Steam developer / publisher | Pawprint Studio | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `appdetails`, accessed 2026-10-11 |
| Authenticode subject | PAWPRINT INTERACTIVE ENTERTAINMENT PTE. LTD., Singapore | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `Get-AuthenticodeSignature` |
| Website on the uninstall entry | `https://www.aniimo.com/` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Unity player | `UnityPlayer.dll` file version `2022.3.62.13482230`; version resource company Unity Technologies; signature Pawprint | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Second ID | `759396` | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Registry suffix. Not a literal string in the binaries searched. Steam `appdetails` `success: false` |

`GameAssembly.dll` beside `UnityPlayer.dll` is the usual Unity IL2CPP layout. That layout reading is <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> **Strongly supported**. The files themselves are confirmed.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📦 Distribution and installation</h2>

<h3 style="text-align:center">Official distribution channels</h3>

Two local installs were running at different times on 2026-10-11.

| Channel | What was verified |
| --- | --- |
| Steam | Manifest app `4126040`, uninstall publisher Pawprint Studio. Process parent `steam.exe` at 17:14:47 |
| Epic Games launcher | Process parent `EpicGamesLauncher.exe` at 17:27:36. `.egstore` catalog item `cb669bb301ab4f6c97d24f98f41cc2e8`, namespace `60a36a8a594c49ab9fb58fa33be6156e`, app name `13ac50a009be47a7a6de50354c19ff5b`. These are catalog ids from the install metadata, not an account id |

The Epic image path was readable. The Steam process image path was not. Both paths are omitted. SHA-256 differs for `Aniimo.exe`, `UnityPlayer.dll`, `GameAssembly.dll`, `NEP2.dll`, `FPX.dll`, and `FPXGame.dll`. `NEPKernel.sys` and `CrashSight64.dll` match. The Epic tree has `FPXEpic.dll` (FunPlus, version `1.0.0.4`), `FPXPluginCore.dll` (version `1.0.0.1`), and Epic-signed `EOSSDK-Win64-Shipping.dll`. It has no `FPXSteam.dll` and no `NEPCleaner.exe`. Epic `fpx_ver.txt` reads `1.0.1392@202609171133`. Epic `FPX.dll` is still version `1.0.0.1392`, product FPX Game SDK, company FUNPLUS, Pawprint signature. `GameAssembly.dll` is 115,180,416 bytes on Epic and 115,180,928 bytes on Steam.

<h3 style="text-align:center">Installation and update behaviour</h3>

| Manifest field | Observed |
| --- | --- |
| `appid` / `name` / `installdir` | `4126040` / `Aniimo` / `Aniimo` |
| `buildid` | `25828794` |
| `SizeOnDisk` | `31405161964` bytes |
| `LastUpdated` | `1791574437` → 2026-10-09 20:33:57 +01 |
| `StateFlags` | `4` (meaning not taken from a Steam specification in this case) |

No updater process other than Steam itself was identified in the launch window.

<h3 style="text-align:center">Files and directories</h3>

Public path form:

```text
<STEAM_LIBRARY>\steamapps\common\Aniimo\
```

| Path (under the install) | Role supported by evidence |
| --- | --- |
| `Aniimo.exe` | Process image name; Pawprint signature; lock file ties the running PID to this tree |
| `UnityPlayer.dll`, `GameAssembly.dll`, `UnityCrashHandler64.exe` | Unity player, IL2CPP game code, crash handler child process |
| `Aniimo_Data\Plugins\x86_64\FPX.dll` and `FPXSteam.dll` | FunPlus version resource; AppsFlyer and `Run`-key strings |
| `FPXGame.dll` | FunPlus version resource; the AppsFlyer needles searched here did not match |
| `CrashSight64.dll`, `CrashSightLog\` | CrashSight SDK; a log from this PID names `pc.crashsight.wetest.net` |
| `GMESDK.dll` | Tencent-signed GME SDK file on disk |
| `discord_partner_sdk.dll`, `discord_krisp.dll` | Present. Process module list was not readable |
| `steam_api64.dll` | Valve-signed Steam API |
| `NEP2.dll`, `NEPKernel.sys`, `NEPCleaner.exe` | Protector component; driver was loaded |
| `gamerun.lock` | Written 2026-10-11 17:15:18 +01; ASCII `pid=4544` and that timestamp |
| `uninstall_cleaner.bat` | Read-only: deletes CrashSight/wesight caches, the lock, and `NEPKernel.sys` |
| `worldx_Data\` | Directory present. FunPlus SDK cache uses the channel name `worldx.global.prod` |
| NVIDIA DLSS / Streamline / Aftermath DLLs | Present; not investigated further |

`verlist.txt` is a short three-field version tuple from the 2026-10-09 update. The middle field is a content checksum and is not republished. `FPX.dll` also contains a Jenkins workspace path from the vendor build. That path is not present on this PC. The drive letter is omitted.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">⚙️ Technical behaviour</h2>

<h3 style="text-align:center">Processes and services</h3>

Collection window 2026-10-11, local time (UTC+1). The Steam process image path was not readable. The Epic process image path was readable and is omitted. Session PIDs are omitted from the Epic rows.

| Time | PID (this session) | Parent | Name | How it is classified |
| --- | --- | --- | --- | --- |
| 17:10:18 | 9964 | — | `explorer.exe` | Shell that later started Steam |
| 17:12:54 | 5380 | `explorer.exe` | `steam.exe` | Steam client |
| 17:12:57 | — | service host / Steam | `steamservice.exe`, `steamwebhelper.exe` | Steam, not the game |
| 17:14:47 | 4544 | `steam.exe` | `Aniimo.exe` | Steam game. Present at 17:26. Gone by 17:34 |
| 17:14:50 | 6532 | `Aniimo.exe` | `UnityCrashHandler64.exe` | Steam crash handler. No TCP sockets in the first snapshot |
| 17:15:07 | 16680 | `steam.exe` | `gameoverlayui64.exe` | Steam overlay |
| 17:27:36 | — | `EpicGamesLauncher.exe` | `Aniimo.exe` | Epic install. Only `Aniimo.exe` at 17:34 |
| 17:27:38 | — | that Epic `Aniimo.exe` | `UnityCrashHandler64.exe` | Epic crash handler |

`NEPKernel` at 17:17:57 and again at 17:20:

| Field | Observed |
| --- | --- |
| State during the Steam session | Running |
| State at 17:34, Epic process only | Stopped. Service image path still the Steam copy of `NEPKernel.sys` |
| Start mode | Manual (`Start` DWORD `3` on the service key) |
| Type | `1` (kernel driver) |
| Image | `\??\<STEAM_LIBRARY>\steamapps\common\Aniimo\NEPKernel.sys` |
| Signature | Valid; `CN=Microsoft Windows Hardware Compatibility Publisher` |
| Version resource | Empty on the `.sys` |
| Strings | `NetEase` is present in `NEP2.dll`, `NEPKernel.sys`, and `NEPCleaner.exe` |

`NEPCleaner.exe` version resource: product NetEase Protector, description NetEase Protector Cleanup Utility, company NetEase, file version `1.0.0.0`. Authenticode subject is NetEase (Hangzhou) Network Co., Ltd. An embedded PDB path contains the directory name `anti-cheat` (`…\anti-cheat\nep-search\NEPUninstaller\…`). That is a build-path string, not a runtime log. The cleaner was not a running process in this window. Whether the game started the driver, or the driver was already running, was not observed: there is no pre-launch sample.

No scheduled task whose name or path matched Aniimo, NEP, FunPlus, Pawprint, CrashSight, or AppsFlyer was returned.

<h3 style="text-align:center">Registry entries and startup behaviour</h3>

**Key:**

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

**Values (public / sanitised):**

| Name | Earlier published data | 2026-10-11 |
| --- | --- | --- |
| `AF_uuid_4126040` | `[REDACTED GUID]` | Still GUID-shaped. Prior GUID was not kept, so sameness is <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |
| `AF_counter_4126040` | `48` | `49` at 17:15:46 and still `49` at 17:22 |
| `AF_uuid_759396` | `[REDACTED GUID]` | Still GUID-shaped |
| `AF_counter_759396` | `0` | `0` |

Machine `Run` keys checked (`HKLM` and `WOW6432Node`) had no `AF_*` or Aniimo-named values.

`HKCU\...\Explorer\StartupApproved\Run` holds 12-byte blobs for the same four names. Byte 0 is `0x03`. Bytes 4–11 are a FILETIME: 2026-09-27 13:23:18 UTC, with the `759396` pair less than one millisecond from the `4126040` pair. The earlier case reported these Startup Apps entries as disabled. The byte value is recorded exactly; calling `0x03` “disabled” is <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> **Strongly supported** by that UI observation plus the blob shape, not by a Microsoft specification quoted in this session.

Other keys inspected only at name level:

| Location | Names worth recording | Values |
| --- | --- | --- |
| `HKCU\Software\Aniimo\Aniimo` | Unity PlayerPrefs, including `BI_AppFirstLaunchSent`, `BI_ActivationFirstLaunchSent`, `fpc_crash_sampling_rate`, `fpc_app_install_ts`, `FPC_AF_REG_LAST_ACCOUNT_V1_<redacted>`, `FPC_AD_FIRST_OPEN_REPORTED_<redacted>` | Not read. `Get-ItemProperty` on the key raised a cast error. Account-like suffixes in names are redacted |
| `HKCU\Software\funplus.sdk` | Value name `orginuuid`; subkey `privacy` | Value data not read |

<h3 style="text-align:center">Network activity</h3>

Point-in-time TCP for the game PID. The local address was a private LAN address and is omitted. Reverse lookups returned no PTR.

The Epic process peers at 17:34, including Cloudflare and Amazon addresses, are in the [endpoint table](privacy-and-telemetry.md#observed-tcp-peers). Steam peers:

**17:17:54 +01**

| State | Remote | Port |
| --- | --- | --- |
| Established | `8.209.80.247` | `20000` |
| Established | `47.245.164.0` (two local ports) | `10000` |
| Established | `43.111.224.154` | `10000` |
| Established | `8.219.197.133` | `10000` |
| Established | `47.90.219.239` | `443` |
| CloseWait | `43.153.246.111` | `443` |

**17:20:58 +01**

| State | Remote | Port |
| --- | --- | --- |
| Established | `47.245.180.250` | `20000` |
| Established | `47.245.164.0` | `10000` |
| Established | `43.111.224.154` | `10000` |
| Established | `8.219.197.133` | `10000` |
| Established | `47.89.139.22` | `443` |
| CloseWait | `47.90.219.239` | `443` |
| CloseWait | `43.153.246.111` | `443` |
| Established | `127.0.0.1` ports `59083` and `59084` | both ends owned by `Aniimo.exe` |

RIPE Stat prefix overview, accessed 2026-10-11: every peer above except `43.153.246.111` and the loopback is in Alibaba ASN `45102` (`ALIBABA-CN-NET`). `43.153.246.111` is in Tencent ASN `132203` (`TENCENT-NET-AP-CN`). Port numbers suggest game services versus HTTPS, and the ASN identifies the prefix holder. They do not identify the application protocol or the bytes sent.

A UDP endpoint was bound on local port `65059` with no remote address in `Get-NetUDPEndpoint`.

`events.appsflyer.com` resolved to `3.174.193.38`, `3.174.193.121`, `3.174.193.122`, and `3.174.193.82`. A later system-wide TCP query showed **zero** connections to those addresses. `pc.crashsight.wetest.net` resolved to `43.163.62.120`, which was not among the game’s snapshot peers. The CrashSight log still names that domain at 17:15:16. See [privacy and telemetry](privacy-and-telemetry.md).

<h3 style="text-align:center">Permissions and persistence</h3>

| Item | Status |
| --- | --- |
| User `Run` values | Present, and StartupApproved is consistent with disabled |
| Kernel driver service | Registered and Running during play; Manual start |
| After the process exits | Not observed |
| Defender exclusions whose path or process matched Aniimo, NEP, FunPlus, CrashSight, or Pawprint | None. Unrelated exclusions are not listed |

Background: [Permissions and persistence](../../concepts/permissions-and-persistence.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">🔐 Security considerations</h2>

- Defender’s operational log, queried for detection IDs over 180 days, returned no events. `Get-MpThreat` and `Get-MpThreatDetection` were empty at 17:17:57. Real-time protection was enabled. That is a statement about those records, not a clearance of the game.
- Event `2010` at 17:14:54 (about seven seconds after launch) is a cloud security-intelligence update. The message does not name Aniimo. The same event ID also fired at other times that afternoon, so the timing is not evidence of a detection.
- A valid Microsoft-signed driver and a NetEase-signed cleaner are consistent with a commercial protector. Kernel code is still a high-privilege component.
- Do not treat `759396`, analytics URLs, or Alibaba-hosted game ports as proof of malice or of innocence.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">⌨️ Diagnostic commands</h2>

Authoritative list: [investigation log](investigation-log.md#commands-used).

Reusable pages: [processes](../../commands/windows/processes.md) · [networking](../../commands/windows/networking.md) · [startup items](../../commands/windows/startup-items.md) · [registry](../../commands/windows/registry.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Aniimo overview](README.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [Investigation log](investigation-log.md)
- [Digital signatures](../../concepts/digital-signatures.md)
