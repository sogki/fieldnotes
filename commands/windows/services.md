<h1 style="text-align:center">Services (Windows)</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Windows commands](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Enumerate Windows services and their state, start mode, and supporting binary paths when investigating persistence or background components.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only (for the query commands on this page)

**Requirements:** Windows; `sc.exe` and/or PowerShell. Listing is often possible without elevation; configuration changes need administrator rights.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — list services</h2>

```powershell
Get-Service | Sort-Object Status, Name | Format-Table -AutoSize
```

<h3 style="text-align:center">What it does</h3>

Lists service names and status.

<h3 style="text-align:center">Expected output</h3>

A table of services. Unknown names should be researched via vendor documentation before assuming malice.

<h3 style="text-align:center">How to interpret it</h3>

Automatic services are a common legitimate persistence mechanism.

<h3 style="text-align:center">Limitations</h3>

Does not cover scheduled tasks, drivers, or helpers without a service.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — query binary path</h2>

```bat
sc qc <ServiceName>
```

<h3 style="text-align:center">What it does</h3>

Shows configuration including `BINARY_PATH_NAME`.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — CIM query with path</h2>

```powershell
Get-CimInstance Win32_Service |
    Select-Object Name, DisplayName, State, StartMode, PathName |
    Format-Table -AutoSize
```

<h3 style="text-align:center">What it does</h3>

| Field | Meaning |
| --- | --- |
| `State` | Running, stopped, and related states |
| `StartMode` | Automatic, manual, disabled, and related modes |
| `PathName` | Image and arguments used to start the service |

<h3 style="text-align:center">How to interpret it</h3>

- Unusual paths under user-writable directories deserve closer inspection; path alone is not proof of malware.
- Correlate with [processes](processes.md) when a service is running.
- Verify `PathName` and digital signature; display names can mislead.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Side effects and risk</h2>

| Action | Risk |
| --- | --- |
| `Get-Service` / `sc qc` / CIM query | <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only |
| `Start-Service` / `Stop-Service` / `sc config` | <img src="../../assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> State-changing |
| Deleting a service | <img src="../../assets/branding/risk-destructive.svg" width="20" height="20" alt="Potentially destructive"> Potentially destructive |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related commands</h2>

- [Processes](processes.md)
- [Startup items](startup-items.md)
- [Permissions and persistence](../../concepts/permissions-and-persistence.md)
- Case study (voluntary filters for Riot Vanguard–related names): [VALORANT & Vanguard — services and drivers](../../applications/valorant-vanguard/services-and-drivers.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📚 Sources</h2>

- Microsoft Learn: `Get-Service`
- Microsoft Learn: `sc` configuration queries
- Microsoft Learn: `Win32_Service` class
