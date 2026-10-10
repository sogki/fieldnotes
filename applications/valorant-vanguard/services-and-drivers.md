<h1 style="text-align:center">VALORANT & Riot Vanguard — services and drivers</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Safe, **read-only** ways to understand Vanguard-related Windows services and drivers using built-in tools. Prefer official documentation in the [technical overview](technical-overview.md) before running anything locally.

> **⚠️ Caution — do not auto-execute**
>
> Fieldnotes agents must not run these commands automatically against a live anti-cheat install. Do not stop, start, disable, or reconfigure services or drivers as part of this investigation.
>
> Read-only queries are lower risk than configuration changes, but they are **not** guaranteed to be ignored by anti-cheat software or free of false-positive risk.

Reusable general reference: [Windows services](../../commands/windows/services.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [What “confirmed” means here](#what-confirmed-means-here)
- [Why both `Win32_Service` and `Win32_SystemDriver`](#why-both-win32_service-and-win32_systemdriver)
- [Local observations](#local-observations)
- [Interpretation (observation vs inference)](#interpretation-observation-vs-inference)
- [A. Inspect relevant Windows services](#a-inspect-relevant-windows-services)
- [B. Inspect registered driver metadata](#b-inspect-registered-driver-metadata)
- [C. Inspect service configuration and status (`sc.exe`)](#c-inspect-service-configuration-and-status-scexe)
- [D. Inspect a driver file’s digital signature](#d-inspect-a-driver-files-digital-signature)
- [E. Inspect driver file metadata](#e-inspect-driver-file-metadata)
- [F. Windows security status checks](#f-windows-security-status-checks)
- [Investigation limitations and safe next steps](#investigation-limitations-and-safe-next-steps)
- [Interpreting missing components](#interpreting-missing-components)
- [Defaults vs local observations](#defaults-vs-local-observations)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">What “confirmed” means here</h2>

| Statement type | Example |
| --- | --- |
| Officially documented | Riot describes a user-mode client and a kernel-mode driver |
| Locally observed | `vgc` / `vgk` rows returned by read-only CIM queries (below) |
| Interpretation | “Stopped + Manual means Pre-Check is on” — <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> until confirmed via Riot UI / eligibility evidence |
| Independently verified | Third-party audit reproducing a specific claim — not assumed here |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Why both `Win32_Service` and `Win32_SystemDriver`</h2>

| Interface | What it inventories | Relevant here |
| --- | --- | --- |
| `Win32_Service` | Registered **Windows services** (Service Control Manager entries) | Returned **`vgc`** (Vanguard client service) |
| `Win32_SystemDriver` | Registered **system drivers** | Returned **`vgk`** (Vanguard kernel driver) |

Checking both matters because:

- A service query alone does **not** fully inventory kernel drivers.
- A driver query alone does **not** describe the user-mode `vgc` service.
- `vgk` was observed through the **driver** inventory, not by treating the first command as a complete kernel catalogue.

Authoritative chronology: [investigation log §8](investigation-log.md#8-local-read-only-inventory-of-vgc-and-vgk).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Local observations</h2>

**Scope:** Read-only CIM queries whose sanitised results were supplied for this case study. **No** service start/stop, driver load/unload, registry edits, or security-feature changes were performed while documenting these results. Fieldnotes did **not** re-execute the commands on the user’s PC for this documentation update.

**Privacy:** Executable and driver locations are sanitised. Usernames, computer names, drive letters, and unrelated inventory are omitted.

<h3 style="text-align:center">User-mode service (`Win32_Service`)</h3>

**Command that produced this observation:**

```powershell
Get-CimInstance Win32_Service |
    Where-Object {
        $_.Name -in @('vgc', 'vgk') -or
        $_.DisplayName -match 'Riot Vanguard'
    } |
    Select-Object Name, DisplayName, State, StartMode, PathName
```

| Property | Observed value | Confidence |
| --- | --- | --- |
| Name | `vgc` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| Display name | `vgc` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| State | `Stopped` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| Startup mode (`StartMode`) | `Manual` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| Reported executable (`PathName`) | Vanguard’s `vgc.exe` (full path redacted as `<VANGUARD_INSTALL>\vgc.exe`) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Path reported by Windows; directory not published |

<h3 style="text-align:center">Kernel-mode driver (`Win32_SystemDriver`)</h3>

**Command that produced this observation:**

```powershell
Get-CimInstance Win32_SystemDriver |
    Where-Object {
        $_.Name -match 'vgk' -or
        $_.DisplayName -match 'Riot Vanguard'
    } |
    Select-Object Name, DisplayName, State, StartMode, PathName
```

| Property | Observed value | Confidence |
| --- | --- | --- |
| Name | `vgk` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| Display name | `vgk` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| State | `Stopped` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| Startup mode (`StartMode`) | `Manual` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| Reported driver (`PathName`) | Vanguard’s `vgk.sys` (full path redacted; commonly under `%windir%\System32\drivers\`) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Path reported by Windows; exact string not published |

> **🔍 Finding**
>
> At the time of inspection, **both** `vgc` and `vgk` were registered with Windows, reported as **Stopped**, and configured for **Manual** startup. Status: **Observed locally — explanation unverified**.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Interpretation (observation vs inference)</h2>

<h3 style="text-align:center">Observed</h3>

- `vgc` was reported as stopped with a Manual startup mode.
- `vgk` was reported as stopped with a Manual startup mode.
- Windows returned a Vanguard-related executable path (`vgc.exe`) and driver path (`vgk.sys`).

<h3 style="text-align:center">Plausible but unverified</h3>

- These results **could be consistent** with Riot’s on-demand operating model (driver not required to launch at every boot; components associated with Riot-title play). See [Pre-Check explanation](technical-overview.md#vanguard-pre-check-and-on-demand-operation).
- Label: <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** — **Observed locally — explanation unverified**.

<h3 style="text-align:center">Not established by these commands</h3>

| Question | Status |
| --- | --- |
| Whether Vanguard Pre-Check is enabled | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |
| Whether the PC satisfies every Pre-Check eligibility requirement | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |
| Why the components were stopped | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |
| Whether Vanguard operates as expected during a game session | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |
| Whether the driver is loaded at another time | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |

> **ℹ️ Note**
>
> Do **not** treat Stopped / Manual as suspicious by itself. Do **not** present on-demand / Pre-Check as the confirmed explanation without Riot UI or other corroborating evidence.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">A. Inspect relevant Windows services</h2>

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only query  
**Elevation:** Usually not required to *list* services; some path details may differ by privilege. Do not elevate unless necessary.

<h3 style="text-align:center">Purpose</h3>

Ask Windows which registered services match common Vanguard-related names or display names.

<h3 style="text-align:center">Command</h3>

```powershell
Get-CimInstance Win32_Service |
    Where-Object {
        $_.Name -in @('vgc', 'vgk') -or
        $_.DisplayName -match 'Riot Vanguard'
    } |
    Select-Object Name, DisplayName, State, StartMode, PathName
```

<h3 style="text-align:center">Parameters / behaviour</h3>

| Piece | Role |
| --- | --- |
| `Win32_Service` | CIM class exposing registered services |
| `Where-Object` | Filters to likely Vanguard-related names |
| `State` | Running, stopped, and related states |
| `StartMode` | Automatic, manual, disabled, and related modes |
| `PathName` | Image path / arguments Windows associates with the service |

<h3 style="text-align:center">Expected output</h3>

Zero or more rows. Empty output is possible if Vanguard is not installed, was removed, or uses unexpected naming.

Sanitised result for this case study: see [Local observations](#local-observations) (`vgc` only from this interface).

<h3 style="text-align:center">How to interpret</h3>

- A **stopped** service is not automatically suspicious.
- `StartMode` reflects configuration, not malice.
- This query alone does **not** fully inventory kernel drivers — use Command B for `vgk`.

<h3 style="text-align:center">Limitations</h3>

- Not a full persistence inventory (scheduled tasks, tray apps, and other helpers may exist separately).
- Absence does not prove Vanguard was never installed.
- Does not modify service configuration.

<h3 style="text-align:center">Side effects</h3>

None intended. Still a process launch / WMI/CIM query that security software *could* notice.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">B. Inspect registered driver metadata</h2>

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only query  
**Elevation:** Listing is often possible without elevation; treat results as incomplete.

<h3 style="text-align:center">Purpose</h3>

Query registered **system driver** records Windows exposes via `Win32_SystemDriver`.

<h3 style="text-align:center">Command</h3>

```powershell
Get-CimInstance Win32_SystemDriver |
    Where-Object {
        $_.Name -match 'vgk' -or
        $_.DisplayName -match 'Riot Vanguard'
    } |
    Select-Object Name, DisplayName, State, StartMode, PathName
```

<h3 style="text-align:center">Expected output</h3>

Zero or more driver records. Do not assume the driver must be **running** merely because Vanguard is installed.

Sanitised result for this case study: see [Local observations](#local-observations) (`vgk` Stopped / Manual).

<h3 style="text-align:center">How to interpret</h3>

| Observation | Reasonable reading |
| --- | --- |
| Driver registered, stopped | Possible depending on mode and timing — not proof of tampering |
| Driver running at boot-start | Consistent with historically documented always-on model — still not a malware verdict |
| No match | Not installed, uninstalled, renamed in a newer build, or filter too narrow |

<h3 style="text-align:center">Limitations</h3>

- Not a complete forensic inventory of every kernel module ever loaded.
- Does not dump kernel memory or prove what the driver *does*.
- Not equivalent to Microsoft’s runtime driver attestation features discussed in Riot’s on-demand article.

<h3 style="text-align:center">Side effects</h3>

Read-only CIM query; same visibility caveat as other diagnostics.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">C. Inspect service configuration and status (`sc.exe`)</h2>

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only for `qc` / `query`  
**Elevation:** Querying often works without elevation; configuration changes require admin and are **out of scope**.

<h3 style="text-align:center">Purpose</h3>

Inspect the `vgc` service’s configuration (`qc`) and runtime status (`query`) **if** that service exists.

<h3 style="text-align:center">Commands</h3>

```powershell
sc.exe qc vgc
sc.exe query vgc
```

Optional parallel for the driver service name when present:

```powershell
sc.exe qc vgk
sc.exe query vgk
```

<h3 style="text-align:center">What these subcommands do</h3>

| Command | Effect |
| --- | --- |
| `sc.exe qc <name>` | Displays configuration (start type, binary path, dependencies) |
| `sc.exe query <name>` | Displays current state (running, stopped, etc.) |

They do **not** start, stop, or reconfigure the service.

<h3 style="text-align:center">Local status for this case study</h3>

`sc.exe` was **not** required for the recorded `vgc` / `vgk` observations (CIM was used). Optional follow-up only.

<h3 style="text-align:center">Limitations</h3>

- Output depends entirely on local install state.
- Do **not** run `sc config`, `sc stop`, or `sc delete` for this investigation.

<h3 style="text-align:center">Side effects</h3>

None for query operations beyond ordinary process execution.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">D. Inspect a driver file’s digital signature</h2>

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only  
**Elevation:** Not required for `Get-AuthenticodeSignature` on a readable file.

<h3 style="text-align:center">Purpose</h3>

Ask Windows for Authenticode signature information on the commonly expected driver path—**only if the file exists**.

<h3 style="text-align:center">Command</h3>

```powershell
$driver = Join-Path $env:windir 'System32\drivers\vgk.sys'

if (Test-Path $driver) {
    Get-AuthenticodeSignature -FilePath $driver |
        Select-Object Status, StatusMessage, SignerCertificate
} else {
    Write-Output 'Driver file not found at the expected location.'
}
```

<h3 style="text-align:center">Concepts to keep separate</h3>

| Concept | Meaning |
| --- | --- |
| **Signature status** | Whether Windows considers the signature valid, missing, or invalid at verification time |
| **Signer / publisher identity** | Subject information on the signing certificate |
| **Trust chain** | Whether intermediate / root CAs are trusted by the local system |

Riot’s 2020 article states the driver was signed with Riot’s EV certificate under Microsoft’s code-signing process. That is a **vendor statement**. Local certificate subject strings must come from an actual inspection—do not invent them.

**Local status:** Signature inspection was **not** performed for this update.

<h3 style="text-align:center">Limitations</h3>

- A valid signature does **not** prove the software is vulnerability-free, privacy-minimal, or aligned with your preferences.
- See [Digital signatures](../../concepts/digital-signatures.md).

<h3 style="text-align:center">Side effects</h3>

Reads file metadata / catalog verification interfaces; does not modify the driver.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">E. Inspect driver file metadata</h2>

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only

<h3 style="text-align:center">Purpose</h3>

Record descriptive file metadata (size, timestamps, version resource) when the expected file exists.

<h3 style="text-align:center">Command</h3>

```powershell
$driver = Join-Path $env:windir 'System32\drivers\vgk.sys'

if (Test-Path $driver) {
    Get-Item $driver |
        Select-Object FullName, Length, CreationTimeUtc, LastWriteTimeUtc

    (Get-Item $driver).VersionInfo |
        Select-Object FileDescription, CompanyName, FileVersion, ProductVersion
} else {
    Write-Output 'Driver file not found at the expected location.'
}
```

<h3 style="text-align:center">How to interpret</h3>

Metadata is **descriptive**, not a full install history. Timestamps can reflect updates, servicing, or copying—not necessarily “first install.”

**Local status:** File version / timestamp inspection was **not** performed for this update.

<h3 style="text-align:center">Privacy</h3>

- Redact usernames, computer names, and unrelated paths.
- Prefer `%windir%\System32\drivers\vgk.sys` style reporting over raw drive letters when possible.
- Do not publish bulk inventories of unrelated applications.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">F. Windows security status checks</h2>

**Risk:** Prefer <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> read-only UI / information tools. Do **not** alter firmware, registry security policy, or driver-signing settings for this case study.

<h3 style="text-align:center">Purpose</h3>

Understand whether the machine presents Secure Boot, TPM, and Memory Integrity / VBS state—features Riot discusses for Pre-Check—without changing them.

<h3 style="text-align:center">Preferred interfaces</h3>

| Check | Suggested interface | Notes |
| --- | --- | --- |
| Secure Boot | System Information (`msinfo32`) — “Secure Boot State” | Read-only system summary |
| TPM | `tpm.msc` or Windows Security / device security UI; or `Get-Tpm` when available | Availability ≠ enabled for every feature |
| Memory Integrity / VBS | Windows Security → Device security → Core isolation; System Information VBS fields | Microsoft documents Memory Integrity as a VBS feature (also called HVCI) |
| Pre-Check activation | Riot **VGTray** (see [technical overview](technical-overview.md#checking-eligibility-or-activation-status)) | Official interface for eligibility / on-demand status |

<h3 style="text-align:center">Example read-only PowerShell probes (optional)</h3>

```powershell
# Secure Boot (may require appropriate firmware/OS support; can error if unsupported)
Confirm-SecureBootUEFI

# TPM presence / readiness (Windows editions with TPM cmdlets)
Get-Tpm

# Device Guard / VBS property surface (availability varies)
Get-CimInstance -ClassName Win32_DeviceGuard -Namespace root\Microsoft\Windows\DeviceGuard |
    Select-Object Version, SecurityServicesRunning, VirtualizationBasedSecurityStatus
```

**Local status:** These probes were **not** run for this documentation update.

<h3 style="text-align:center">Side effects</h3>

Queries should not change configuration. **Do not** follow with registry edits, `bcdedit` changes, or BIOS toggles as part of this investigation.

Microsoft references:

- [Memory integrity and VBS](https://learn.microsoft.com/en-us/windows-hardware/drivers/bringup/device-guard-and-credential-guard)
- [Enable memory integrity](https://learn.microsoft.com/en-us/windows/security/hardware-security/enable-virtualization-based-protection-of-code-integrity) (feature documentation—not an instruction to enable it here)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Investigation limitations and safe next steps</h2>

**Completed for this update:** read-only Windows CIM queries for `vgc` / `vgk` (results sanitised above). **No** system configuration was changed during those checks.

**Potential follow-up (not completed):**

1. Review Riot’s official Pre-Check / on-demand guidance ([on-demand article](https://www.riotgames.com/en/news/vanguard-on-demand) · [What is Vanguard?](https://support-valorant.riotgames.com/hc/en-us/articles/360046160933-What-is-Riot-Vanguard)).
2. If the user chooses, inspect Vanguard’s existing **VGTray** interface for eligibility and activation status **without changing settings**.
3. Record any further observations with timestamps and sanitised command output in the [investigation log](investigation-log.md).
4. Reassess the on-demand interpretation only when additional evidence is available (for example Pre-Check status from VGTray, or a later CIM snapshot during / after a Riot game session—still without interfering with anti-cheat).

Still **out of scope:** stopping services, manipulating drivers, modifying the registry, disabling security features, installing diagnostic utilities, attaching debuggers, or testing detection evasion.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Interpreting missing components</h2>

| Situation | Plausible explanations | Label |
| --- | --- | --- |
| No `vgc` / `vgk` | Never installed; uninstalled; install incomplete; naming changed | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> until correlated with Riot Client / Apps & Features |
| File missing at expected `vgk.sys` location | Not installed; different path; cleanup after uninstall | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> |
| Service present, file missing | Broken install or partial removal | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> — not a malware conclusion by itself |
| Driver stopped while game not running | Compatible with on-demand / idle scenarios depending on mode | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> without product-mode confirmation |

Absence of artefacts is **not** proof that Vanguard never ran, and presence is **not** proof of malice.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Defaults vs local observations</h2>

| Topic | Documented / expected default (public) | Local observation in this repo |
| --- | --- | --- |
| `vgc` registered | Common for installs that include Vanguard | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Present; Stopped; Manual |
| `vgk` registered | Common for installs that include Vanguard | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Present (via `Win32_SystemDriver`); Stopped; Manual |
| Boot-start vs on-demand | Both described by Riot depending on Pre-Check | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Activation state unknown; Stopped/Manual is <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> consistent with on-demand |
| Publisher string on `vgk.sys` | Riot describes EV signing process | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Signature check not performed here |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- [What is Vanguard?](https://support-valorant.riotgames.com/hc/en-us/articles/360046160933-What-is-Riot-Vanguard) (re-verified 2026-10-10)
- [Incoming: Vanguard On-Demand](https://www.riotgames.com/en/news/vanguard-on-demand) (re-verified 2026-10-10)
- Microsoft Learn: [`Win32_Service`](https://learn.microsoft.com/en-us/windows/win32/cimwin32prov/win32-service), [`Win32_SystemDriver`](https://learn.microsoft.com/en-us/windows/win32/cimwin32prov/win32-systemdriver)
- Fieldnotes: [Services](../../commands/windows/services.md), [Digital signatures](../../concepts/digital-signatures.md)
- Case overview source list: [README sources](README.md#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview — Pre-Check](technical-overview.md#vanguard-pre-check-and-on-demand-operation)
- [Privacy and security](privacy-and-security.md)
- [Investigation log](investigation-log.md)
