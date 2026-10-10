<h1 style="text-align:center">Registry (Windows)</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Windows commands](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Read registry keys and values during investigation without changing system state.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only (for the query commands on this page)

**Requirements:** Windows; `reg.exe` and/or PowerShell. `HKCU` usually readable by the current user; some `HKLM` paths need elevation.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — query a key</h2>

```bat
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Run"
```

<h3 style="text-align:center">What it does</h3>

Reads and prints key contents (value names, types, and data).

<h3 style="text-align:center">Expected output</h3>

Value listings such as `REG_SZ` entries. Missing values produce an error.

<h3 style="text-align:center">How to interpret it</h3>

- Record **name**, **type**, and **full data** before hypothesising purpose (redact before publishing).
- Presence of a `Run` entry does not establish that associated software is malicious.

<h3 style="text-align:center">Limitations</h3>

- Snapshot in time; no writer process; no history without other auditing

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — read selected PowerShell properties</h2>

```powershell
$path = 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run'

Get-ItemProperty -Path $path |
    Select-Object AF_uuid_4126040, AF_counter_4126040,
                  AF_uuid_759396, AF_counter_759396 |
    Format-List
```

<h3 style="text-align:center">What it does</h3>

Reads specific value names as object properties without modifying them.

> **ℹ️ Note**
>
> When publishing notes, omit unrelated startup entries and personal paths. Redact GUID-like identifiers as `[REDACTED GUID]`.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

Case results: [Aniimo investigation log](../../applications/aniimo/investigation-log.md#b-inspect-the-four-specific-run-values).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — inspect all values in a key</h2>

```powershell
Get-ItemProperty `
    'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' |
    Format-List *
```

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — attempt last-write time</h2>

```powershell
Get-Item 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' |
    Select-Object Name, LastWriteTime
```

<h3 style="text-align:center">What it does</h3>

Attempts to expose key metadata. `LastWriteTime` may be blank depending on environment.

> **❓ Unverified**
>
> In the Aniimo case study, `LastWriteTime` was not populated. Do not invent timestamps when the property is blank.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Side effects and risk</h2>

| Action | Risk |
| --- | --- |
| `reg query` / `Get-ItemProperty` / `Get-Item` | <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only |
| `reg add` / `reg delete` / property set/remove | <img src="../../assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> / <img src="../../assets/branding/risk-destructive.svg" width="20" height="20" alt="Potentially destructive"> |

Optional backup before any modification:

```bat
reg export "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" run-backup.reg
```

`reg export` is <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> relative to system behaviour (writes a file you choose). Do not commit exports that contain personal paths or secrets.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related commands</h2>

- [Startup items](startup-items.md)
- [Processes](processes.md)
- [Aniimo investigation log](../../applications/aniimo/investigation-log.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📚 Sources</h2>

- Microsoft Learn: `reg` command reference
- Microsoft Learn: Registry hives and data types
