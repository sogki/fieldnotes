<h1 style="text-align:center">Startup items (Windows)</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Windows commands](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Inventory programmes and commands configured to run at user logon or system startup, including values under common `Run` registry keys.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only (for the query commands on this page)

**Requirements:** Windows; Command Prompt or PowerShell. Standard user for current-user startup; administrator may be required for some machine-wide views.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — enumerate via CIM</h2>

```powershell
Get-CimInstance Win32_StartupCommand |
    Select-Object Name, Command, Location, User |
    Format-List
```

<h3 style="text-align:center">What it does</h3>

- Queries startup commands exposed by the Windows CIM / WMI provider
- Returns name, command data, location, and user when available

<h3 style="text-align:center">Expected output</h3>

Objects listing registered startup commands. Some entries may store data that does **not** look like an ordinary executable path (for example GUID-style strings or bare numbers).

> **ℹ️ Note**
>
> For observed `AF_*` results from the Aniimo case study, see the [investigation log](../../applications/aniimo/investigation-log.md#commands-used)—case output is not duplicated here.

<h3 style="text-align:center">How to interpret it</h3>

- Unusual data warrants careful interpretation; confirm whether Windows treats it as a launch string.
- Presence in Startup Apps / Task Manager often correlates with registered startup entries; the UI alone does not prove malice.

<h3 style="text-align:center">Limitations</h3>

- Not a complete inventory of all persistence mechanisms
- Does not identify which process created an entry

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — filter by name pattern</h2>

```powershell
Get-CimInstance Win32_StartupCommand |
    Where-Object { $_.Name -like 'AF_*' } |
    Format-List Name, Command, Location, User
```

<h3 style="text-align:center">What it does</h3>

Isolates startup commands whose names match a pattern. Adjust `-like` for the investigation.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — registry `Run` keys</h2>

```bat
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Run"
reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\Run"
```

<h3 style="text-align:center">What it does</h3>

- `HKCU\...\Run` — current user (typically at that user’s logon)
- `HKLM\...\Run` — machine-wide (typically each logon)

Elevated prompt may be required for some `HKLM` reads.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — startup folders</h2>

```powershell
Get-ChildItem "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup"
Get-ChildItem "$env:ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
```

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Side effects and risk</h2>

| Action | Risk |
| --- | --- |
| Query / list commands above | <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only |
| Deleting or modifying `Run` values | <img src="../../assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> State-changing |
| Disabling startup apps via Settings or Task Manager | <img src="../../assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> State-changing |

> **⚠️ Caution**
>
> Do not delete values until you have recorded name, type, and data, and understand the application impact.

> **ℹ️ Note**
>
> Unfamiliar registry entries and analytics-like identifiers do not, on their own, demonstrate malicious activity.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related commands</h2>

- [Registry](registry.md)
- [Processes](processes.md)
- [Services](services.md)
- [Aniimo investigation log](../../applications/aniimo/investigation-log.md)
- [Permissions and persistence](../../concepts/permissions-and-persistence.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📚 Sources</h2>

- Microsoft Learn: Run and RunOnce registry keys
- Microsoft Learn: `Win32_StartupCommand` class documentation
