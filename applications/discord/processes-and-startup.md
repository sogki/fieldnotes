<h1 style="text-align:center">Discord — processes and startup</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Local process tree and startup behaviour observed 2026-10-10 UTC while Stable was already running (not launched by the investigation).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Process snapshot](#process-snapshot)
- [Startup and launch path](#startup-and-launch-path)
- [Updates](#updates)
- [Services](#services)
- [Commands used](#commands-used)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Process snapshot</h2>

Six `Discord.exe` processes from `%LOCALAPPDATA%\Discord\app-1.0.9261\`:

| Role | Observation | Confidence |
| --- | --- | --- |
| Main / browser process | One parent `Discord.exe` whose parent updater process had already exited | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Process tree |
| Child helpers | Five child `Discord.exe` under that parent (renderer/GPU-style helpers) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Parent/child links |

`SquirrelSetup.log` (same morning): `Starting Squirrel Updater: --processStart Discord.exe` then launch of `app-1.0.9261\Discord.exe`.

| Claim | Label |
| --- | --- |
| Stable uses a multi-process desktop model | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Consistent with Chromium/Electron process model | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Strongly supported |
| Exact Chromium role of each child PID | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not labelled via `--type=` inspection here |
| Multiple processes are inherently malicious | **Not claimed** |

No `Update.exe` process was running at the snapshot (updater had already exited after start).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Startup and launch path</h2>

| Artefact | Value (sanitised) | Confidence |
| --- | --- | --- |
| `HKCU\…\Run` `Discord` | `"<USERPROFILE>\AppData\Local\Discord\Update.exe" --processStart Discord.exe` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Windows services named Discord | **None** | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Narrow service query |

Launch chain:

```text
Run key / user start
  → Update.exe --processStart Discord.exe
    → app-<version>\Discord.exe (main)
      → child Discord.exe processes
```

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Updates</h2>

| Evidence | Detail | Confidence |
| --- | --- | --- |
| `packages\` directory | Present | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `SquirrelSetup.log` | Repeated `--processStart` / About to launch lines; version moved `1.0.9260` → `1.0.9261` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Vendor blog | Existing updater used for architecture transitions | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official blog |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Services</h2>

No Discord Win32 services matched name/display filters at snapshot time.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Commands used</h2>

Risk: <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only**.

```powershell
Get-CimInstance Win32_Process |
  Where-Object { $_.Name -match '^(Discord|Update)\.exe$' -and $_.ExecutablePath -match 'Discord\\' } |
  Select-Object Name, ProcessId, ParentProcessId, ExecutablePath

Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' |
  Select-Object Discord

Get-Content "$env:LOCALAPPDATA\Discord\SquirrelSetup.log" -Tail 20
```

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Local CIM + Run key + SquirrelSetup.log (2026-10-10)
- [Processes](../../commands/windows/processes.md)
- [Startup items](../../commands/windows/startup-items.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Investigation log](investigation-log.md)
