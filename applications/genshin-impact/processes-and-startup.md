<h1 style="text-align:center">Genshin Impact — processes and startup</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Read-only process, service, driver, and startup observations from 2026-10-10 UTC. The game and HoYoPlay were **not** launched for this investigation.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Process snapshot](#process-snapshot)
- [Services and drivers](#services-and-drivers)
- [Startup entries](#startup-entries)
- [Expected runtime model](#expected-runtime-model)
- [Commands used](#commands-used)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Process snapshot</h2>

| Filter | Result | Confidence |
| --- | --- | --- |
| Names matching `Genshin`, `HoYo`, `miHoYo`, `HYP` | **No matching processes** | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local CIM |
| Interpretation | Launcher/game not running at snapshot time | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Observation |

An empty process list is **not** evidence the software is uninstalled.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Services and drivers</h2>

| Name | State | StartMode | Image | Confidence |
| --- | --- | --- | --- | --- |
| `HoYoProtect` | **Stopped** | **Manual** | `\??\C:\WINDOWS\system32\HoYoKProtect.sys` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `Win32_SystemDriver` |

No Windows **services** matched narrow `mhy|hoyo|genshin|hk4e|cognosphere` name/display filters at snapshot time.

| Claim | Label |
| --- | --- |
| Kernel anti-cheat driver is registered on this PC | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Driver was not running while the game was closed | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Driver never runs except during gameplay | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** — not proven across reboot/update scenarios |
| Stopped driver is suspicious or proves malice | **Not claimed** |

File product metadata: Product `HoYoProtect`, Company `miHoYo`, FileVersion `3.2`. Authenticode via Microsoft Hardware Compatibility Publisher (<img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Startup entries</h2>

| Entry | Observed? | Confidence |
| --- | --- | --- |
| HoYoPlay / Genshin under `HKCU\…\Run` | **No** | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Narrow Run-key query |
| `Win32_StartupCommand` matching HoYo/Genshin | **No** | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |

Discord startup entries were present on the same machine but belong to the Discord case study.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Expected runtime model</h2>

When a user later launches the title (not done here):

1. HoYoPlay (`launcher.exe` / `HYP.exe`) starts (CEF/Qt UI).  
2. Updater components may run.  
3. Game `GenshinImpact.exe` starts from `GameInstallPath`.  
4. `HoYoProtect` may start (expected for anti-cheat; **not observed in this snapshot**).

Treat steps 3–4 as <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** product behaviour until observed live.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Commands used</h2>

Risk: <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**. Sanitised patterns:

```powershell
Get-CimInstance Win32_Process |
  Where-Object { $_.Name -match 'Genshin|HoYo|miHoYo|HYP' } |
  Select-Object Name, ProcessId, ParentProcessId, ExecutablePath

Get-CimInstance Win32_SystemDriver |
  Where-Object { $_.Name -match 'mhy|hoyo|genshin|hk4e' }

Get-CimInstance Win32_StartupCommand |
  Where-Object { $_.Command -match 'HoYo|Genshin|miHoYo' }
```

Full chronology: [investigation log](investigation-log.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Local CIM/registry/filesystem observations (2026-10-10)
- [Services (Windows)](../../commands/windows/services.md)
- [Processes (Windows)](../../commands/windows/processes.md)
- [Startup items](../../commands/windows/startup-items.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
