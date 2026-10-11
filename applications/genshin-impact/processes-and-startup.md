<h1 style="text-align:center">Genshin Impact — processes and startup</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Process, driver, and startup notes. The 2026-10-10 snapshot was taken while the game was closed. On 2026-10-11 HoYoPlay and the game were already running when the first process list was taken.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Play session on 2026-10-11](#play-session-on-2026-10-11)
- [Process tree](#process-tree)
- [Mid-session check at 16:54Z](#mid-session-check-at-1654z)
- [After the game exited](#after-the-game-exited)
- [Driver](#driver)
- [Startup and services](#startup-and-services)
- [Microsoft Defender](#microsoft-defender)
- [Earlier idle snapshot](#earlier-idle-snapshot)
- [Commands](#commands)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Play session on 2026-10-11</h2>

HoYoPlay and the game were already running. They were not started for these notes. Times below are UTC. The machine timezone was UTC+1, so 16:42Z is 17:42 local.

| Event | Time (UTC) | Evidence |
| --- | --- | --- |
| Parent process `22992` writes HoYoPlay startup lines, including a `CreateProcess` of `HYSafeMode.exe` | 16:42:08Z | `launcher.log` lines tagged with that pid. Image path of 22992 was **not** captured; it had exited before the process query |
| `HYP.exe` starts. Parent pid `22992` | 16:42:08Z | `Win32_Process` creation time and later image path |
| Four `HYPHelper.exe` processes start, parent `HYP.exe` | 16:42:09Z | Creation times |
| `GenshinImpact.exe` starts, parent `HYP.exe` | 16:42:33Z | Creation time and image path |
| `ZFGameBrowser.exe` tree starts, parent `GenshinImpact.exe` | 16:44:43Z | `Get-Process` start times |
| First connection sample | about 16:45Z | `Get-NetTCPConnection` |
| Mid-session check. Game, launcher, helpers, browser, and `HoYoProtect` still present | 16:54:57Z | Process and driver query |
| Game quit. `output_log.txt` records `Call QuitApplication` | 17:39:40Z | Shared-read tail of `output_log.txt` |
| Game and `ZFGameBrowser.exe` gone. Launcher still up. `HoYoProtect` Stopped | 17:39:56Z–17:40:08Z | `Get-Process`, `sc.exe query HoYoProtect` |

There is no sample from immediately before launch, so the change from Stopped to Running was not seen. The stop after the game exited is in the next section.

`Win32_Process.ExecutablePath` and `CommandLine` were blank for these processes. `QueryFullProcessImageName` with `PROCESS_QUERY_LIMITED_INFORMATION` (`0x1000`) returned paths. Blank WMI fields are recorded as an access limitation. They are not, by themselves, proof of a protected-process policy.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Process tree</h2>

| Process | Image | Parent | Role established by evidence |
| --- | --- | --- | --- |
| pid 22992 (exited) | Not captured | Unknown | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Wrote `launcher.log` startup lines at the same second `HYP.exe` started, and was `HYP.exe`'s parent |
| `HYP.exe` | `C:\Program Files\HoYoPlay\1.18.0.380\HYP.exe` | 22992 | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Launcher image. Authenticode-valid Cognosphere binary, file version `1.18.0.380` |
| `HYPHelper.exe` ×4 | Same directory, `HYPHelper.exe` | `HYP.exe` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Four children. Command lines were not readable, so GPU / renderer / utility roles were **not** distinguished. One of them owned HTTPS sockets in the first sample; the other three did not, at that instant |
| `GenshinImpact.exe` | `<DRIVE>:\Genshin Impact game\GenshinImpact.exe` | `HYP.exe` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> The active install from `GameInstallPath`, not the older 5.1.0 tree |
| `ZFGameBrowser.exe` ×6 | `<DRIVE>:\Genshin Impact game\GenshinImpact_Data\Plugins\ZFGameBrowser.exe` | First child of the game; five grandchildren of that browser process | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> In-game browser image, signed by Cognosphere. No TCP endpoints were owned by these pids in the samples after they had started |

Not present in any 2026-10-11 snapshot: `HYPWorker.exe`, `HYUpdater.exe`, `HYSafeMode.exe`, `crashreport.exe`, `upload_crash.exe`. The launcher log records a `CreateProcess` of `HYSafeMode.exe` at startup. That process was not in the later lists, so a short-lived start cannot be ruled out and cannot be confirmed.

Working sets at the first sample, for scale only: launcher about 159 MB, helpers from about 23 MB to 123 MB, game about 3.3 GB.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Mid-session check at 16:54Z</h2>

At 16:54:57Z the game had not been closed:

| Name | Count |
| --- | --- |
| `GenshinImpact` | 1 |
| `HYP` | 1 |
| `HYPHelper` | 4 |
| `ZFGameBrowser` | 6 |
| `HoYoProtect` | Running, start mode Manual |

That table is the 16:54Z snapshot. The exit sample is the next section.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">After the game exited</h2>

The game closed at about 17:39Z. HoYoPlay stayed open. Neither process was closed for these notes.

| Name | At 17:39:56Z |
| --- | --- |
| `GenshinImpact` | Gone. The port `8999` socket was gone with it |
| `ZFGameBrowser` | Gone. No `HYUpdater`, `crashreport`, or `upload_crash` |
| `HYP` | Still pid 16456, started 16:42:08Z |
| `HYPHelper` | Still the same four pids from 16:42:09Z |
| `HoYoProtect` | **Stopped**, Manual, at the 17:40:08Z query |

`output_log.txt` grew to 348,398 bytes at 17:39:40Z. The new lines are quit and shutdown messages, including `HoYoPass_Uninitialize call success` and `zfb_shutdown`. They do not name a driver.

Within a few seconds of that quit line, the launcher opened new port-443 sockets, including `163.181.243.199` and CloudFront-prefix addresses. Those are launcher sockets. Their hostnames were not captured.

| Claim | Label |
| --- | --- |
| After this exit, the game and in-game browser were gone and the launcher remained | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `HoYoProtect` was Stopped while `HYP.exe` was still running | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `sc.exe query` at 17:40:08Z |
| The game exit is what unloaded the driver | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> It was Running during play and Stopped after the quit log, with the launcher still up. The unload call was not traced |
| `sc.exe` Win32 exit code 31 (`0x1f`) means the unload failed | **Not claimed**. The service state was STOPPED. The code was not investigated |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Driver</h2>

| Field | Value | Source |
| --- | --- | --- |
| Service name | `HoYoProtect` | `sc.exe query`, `Win32_SystemDriver` |
| Display name | `HoYoProtect` | `sc.exe qc` |
| Type | `1` kernel driver | Registry and `sc qc` |
| Start | `3` demand start (`SERVICE_DEMAND_START`) | Registry value `Start` and `sc qc` |
| Error control | `1` normal | Registry |
| Image | `\??\C:\WINDOWS\system32\HoYoKProtect.sys` | Registry `ImagePath` |
| State during play | **Running**, stoppable, not pausable, ignores shutdown | `sc.exe query` at 16:45Z and still Running at 16:54Z |
| State after the game exited | **Stopped**, Manual. Win32 exit code 31 (`0x1f`) | `sc.exe query` at 17:40:08Z, launcher still running |
| File version | Product `HoYoProtect`, company `miHoYo`, file version `3.2`, product version `3.2.40412892` | Version resource |
| Link date reported by `driverquery` | 29/07/2026 05:00:35 | `driverquery /v` |
| Authenticode | Valid, CN `Microsoft Windows Hardware Compatibility Publisher` | `Get-AuthenticodeSignature` |
| SHA-256 | `3C5129295AD93E9AD4FABC54940955915E79375C43A778E097DEC2DE486684DD` | Identical for `system32` and the copy in the active game folder |

| Claim | Label |
| --- | --- |
| A kernel driver registered as `HoYoProtect` was loaded while the game process was running | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Its configured start mode is demand start, not boot or automatic | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| The game or launcher is what loaded it | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> It was Running during play. The start was not watched. After exit it was Stopped while the launcher was still running, so the load is not tied to HoYoPlay staying open |
| A loaded anti-cheat driver is evidence of malware | **Not claimed** |
| Demand start means the driver can never be running at boot | **Not claimed**. Start mode describes the configuration. It was not re-checked across a reboot |

`driverError.log` in the game folder contains a line from **2026-03-23**: `initDriver Failed: Error [5,1110,3]`. Windows error 5 is access denied. That is a historical log line. The driver was Running during the 2026-10-11 play session and Stopped after the 17:39Z exit.

No other kernel driver with a HoYo, miHoYo, Genshin, or `hk4e` name was returned by `Win32_SystemDriver`. No user-mode service matched those names.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Startup and services</h2>

| Check | Result | Confidence |
| --- | --- | --- |
| `HKCU`, `HKLM`, and `WOW6432Node` `Run` values whose data matches HoYo, Genshin, miHoYo, Cognosphere, or HYP | No match | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Per-user and common Startup folders, same name filter | No match | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `Get-ScheduledTask` name or path filter for those tokens | No match | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `HKCU\Software\Classes` key names matching genshin, hoyoplay, or mihoyo | No match | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Full `HKCR` association search | Not done | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |
| Firewall application-filter enumeration | Not completed | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |

The driver registration itself is persistent configuration. Whether the driver stays loaded after exit is separate: at 17:40Z, with the game closed and HoYoPlay still open, `HoYoProtect` was Stopped.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Microsoft Defender</h2>

`Get-MpComputerStatus` on 2026-10-11:

| Field | Reported value |
| --- | --- |
| AM service, antivirus, real-time protection, IOAV, NIS | Enabled |
| Antivirus signature age | 0 |
| Quick scan age | 0 |
| Full scan age | 830 |

`Get-MpThreatDetection` returned **zero** detections. A filter for Genshin, HoYo, miHoYo, Cognosphere, and `HoYoKProtect` therefore also matched nothing. `Get-MpPreference` exclusion paths and processes had no match for those tokens. `Get-WinEvent` for Defender operational event id **1116** over the previous 365 days returned no matching events.

| Claim | Label |
| --- | --- |
| Defender reported no current threat detection and no 1116 event in that query | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> This is a statement about the tool output |
| Every game file has been scanned and cleared | **Not established** |
| Absence of a detection proves the software is harmless | **Not claimed** |

No other installed security product was queried.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Earlier idle snapshot</h2>

On 2026-10-10, with no Genshin or HoYoPlay process running, `HoYoProtect` was **Stopped** and **Manual**. No HoYo or Genshin `Run` value was present. That remains a valid observation for that moment. It is not a before-image of the 2026-10-11 launch.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Commands</h2>

Risk: <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**.

```powershell
Get-CimInstance Win32_Process |
  Where-Object { $_.Name -match 'Genshin|HoYo|miHoYo|HYP' } |
  Select-Object Name, ProcessId, ParentProcessId, CreationDate

# Image path when WMI ExecutablePath is blank.
# PROCESS_QUERY_LIMITED_INFORMATION = 0x1000
# QueryFullProcessImageName on the handle from OpenProcess.

Get-CimInstance Win32_SystemDriver -Filter "Name='HoYoProtect'"
sc.exe query HoYoProtect
sc.exe qc HoYoProtect

Get-MpComputerStatus
Get-MpThreatDetection
Get-MpPreference | Select-Object ExclusionPath, ExclusionProcess
```

`OpenProcess` here only requests limited information so the image path can be read. It does not debug or modify the process.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Local observations, 2026-10-11 and 2026-10-10 — [investigation log](investigation-log.md)
- [Services (Windows)](../../commands/windows/services.md)
- [Processes (Windows)](../../commands/windows/processes.md)
- [Startup items](../../commands/windows/startup-items.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
