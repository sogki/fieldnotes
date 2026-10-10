<h1 style="text-align:center">Networking (Windows)</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Windows commands](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Observe listening ports and established connections, and correlate them with owning processes during application investigation.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only (for the query commands on this page)

**Requirements:** Windows; `netstat` and/or PowerShell `Get-NetTCPConnection`. Viewing all PIDs with `netstat -b` or `-o` may require an elevated prompt.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — active connections with PIDs</h2>

```bat
netstat -ano
```

<h3 style="text-align:center">What it does</h3>

| Parameter | Meaning |
| --- | --- |
| `-a` | Listening and connected endpoints |
| `-n` | Numeric addresses and ports (no DNS lookup) |
| `-o` | Owning process ID |

<h3 style="text-align:center">Expected output</h3>

Rows of protocol, local address, remote address, state, and PID. Many entries are routine OS or application traffic.

<h3 style="text-align:center">How to interpret it</h3>

- Correlate PID with `ExecutablePath` via [process commands](processes.md) before judging an application.
- CDN and update endpoints are common for legitimate software.
- Analytics traffic ≠ malware.

<h3 style="text-align:center">Limitations</h3>

- Point-in-time; short connections can be missed
- Encrypted payloads are not visible

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — executable names for endpoints</h2>

```bat
netstat -abn
```

<h3 style="text-align:center">What it does</h3>

`-b` displays the executable involved. It can be slow and typically requires elevation.

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Command — PowerShell TCP view</h2>

```powershell
Get-NetTCPConnection |
    Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, OwningProcess |
    Format-Table -AutoSize
```

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Side effects and risk</h2>

| Action | Risk |
| --- | --- |
| `netstat` / `Get-NetTCPConnection` | <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only |
| Firewall rule changes, stopping adapters | <img src="../../assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> State-changing |
| Packet capture storage | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> observation, but files may hold sensitive data |

> **⚠️ Caution**
>
> Redact tokens, cookies, and personal identifiers. Do not commit raw captures containing secrets. This page does not document downloading or executing remote scripts.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related commands</h2>

- [Processes](processes.md)
- [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)
- [Aniimo privacy and telemetry](../../applications/aniimo/privacy-and-telemetry.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📚 Sources</h2>

- Microsoft Learn: `netstat`
- Microsoft Learn: `Get-NetTCPConnection`
