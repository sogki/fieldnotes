<h1 style="text-align:center">Processes (Windows)</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Windows commands](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

List running processes and inspect basic attributes (name, PID, path) while investigating application behaviour.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only (for the listing commands on this page)

**Requirements:** Windows; Tasklist and/or PowerShell. Some path or owner fields may need elevation or remain inaccessible for protected processes.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — list processes</h2>

```bat
tasklist
```

<h3 style="text-align:center">What it does</h3>

Prints a basic process table (image name, PID, session, memory).

<h3 style="text-align:center">Expected output</h3>

A list of running processes. Paths are not included in the basic form.

<h3 style="text-align:center">How to interpret it</h3>

Absence of a named process at check time does not prove it never ran.

<h3 style="text-align:center">Limitations</h3>

Point-in-time view; short-lived helpers may exit before listing.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — list with image path and command line</h2>

```powershell
Get-CimInstance Win32_Process |
    Select-Object ProcessId, Name, ExecutablePath, CommandLine |
    Format-Table -AutoSize
```

<h3 style="text-align:center">What it does</h3>

| Field | Use |
| --- | --- |
| `ProcessId` | Correlate with network sockets |
| `ExecutablePath` | Locate binaries for signature checks |
| `CommandLine` | Reveal paths or flags (`CommandLine` availability varies) |

<h3 style="text-align:center">How to interpret it</h3>

- Prefer path and signature checks over process name alone (names can be reused).
- Match unexpected network activity to a PID, then to `ExecutablePath`.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — filter by name</h2>

```powershell
Get-CimInstance Win32_Process -Filter "Name = 'steam.exe'" |
    Select-Object ProcessId, Name, ExecutablePath, CommandLine
```

Replace the filter with the process name under investigation.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Side effects and risk</h2>

| Action | Risk |
| --- | --- |
| Listing / querying processes | <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only |
| `taskkill` / `Stop-Process` | <img src="../../assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> State-changing — can lose data or destabilise sessions |

> **⚠️ Caution**
>
> Stopping processes is outside the read-only investigation flow documented here.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related commands</h2>

- [Networking](networking.md)
- [Services](services.md)
- [Digital signatures](../../concepts/digital-signatures.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📚 Sources</h2>

- Microsoft Learn: `tasklist`
- Microsoft Learn: `Win32_Process` class
