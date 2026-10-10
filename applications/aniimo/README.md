<h1 style="text-align:center"><img src="../../assets/branding/icon-game.svg" alt="" width="40" height="40" style="vertical-align:middle;margin-right:10px">Aniimo</h1>

<a href="../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Applications</a> · <a href="../../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes</a>

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">


Windows Startup Apps entries led to four current-user `Run` registry values named `AF_uuid_*` / `AF_counter_*`. One numeric suffix matches Aniimo’s Steam application ID (`4126040`).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Overview</h2>

| Field | Value | Confidence |
| --- | --- | --- |
| Application in scope | Aniimo (Steam app ID `4126040`) | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> User confirmed install/play; Steam metadata included `4126040` |
| Platform | Windows, current-user startup / registry | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Recorded observations |
| Artefacts | Four `AF_*` values under `HKCU\...\Run` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> GUID data redacted publicly |
| Writer / SDK attribution | AppsFlyer-like naming from community reports | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> Not independently verified |
| Malware determination | None made | Explicitly **not** claimed |

> **🕵️ Investigation status**
>
> Developing case study — **not** a completed security audit. Unfamiliar registry entries and analytics-related identifiers do not, on their own, demonstrate malicious activity.

| Companion | Purpose |
| --- | --- |
| <a href="technical-overview.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Technical overview</a> | Identification and technical context |
| <a href="privacy-and-telemetry.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Privacy and telemetry</a> | Possible analytics connection and limits |
| <a href="investigation-log.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log</a> | **Authoritative** chronology and commands |

<h3 style="text-align:center">On this page</h3>

- [Technical sections](#technical-sections)
- [Security considerations](#security-considerations)
- [Diagnostic commands](#diagnostic-commands)
- [Findings and unresolved questions](#findings-and-unresolved-questions)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Technical sections</h2>

<h3 style="text-align:center">Developer and publisher</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** pending confirmation from official Steam / publisher metadata.

<h3 style="text-align:center">Official distribution channels</h3>

| Channel | Notes | Confidence |
| --- | --- | --- |
| Steam | App ID `4126040`; user installed and played via Steam; library metadata included this ID | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> |

Other channels: <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown**.

<h3 style="text-align:center">Installation and update behaviour</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** beyond the user’s report of installing and playing through Steam. No install-time tracing is recorded.

<h3 style="text-align:center">Files and directories</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown.** No executable or install-tree analysis was performed.

See [follow-up research](investigation-log.md#follow-up-research-not-yet-performed).

<h3 style="text-align:center">Processes and services</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown.** No process or service attribution was performed.

<h3 style="text-align:center">Registry entries and startup behaviour</h3>

Located under:

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

| Value name | Public documentation | Notes |
| --- | --- | --- |
| `AF_uuid_4126040` | `[REDACTED GUID]` | Suffix matches Aniimo Steam app ID |
| `AF_counter_4126040` | `48` | Meaning <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |
| `AF_uuid_759396` | `[REDACTED GUID]` | Suffix origin <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |
| `AF_counter_759396` | `0` | Meaning <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |

> **🔍 Finding**
>
> Entries appeared as disabled Startup Apps items in Task Manager, without an ordinary executable path or identifiable publisher in that UI. Stored data did not resemble ordinary executable command lines.

Full command history and Steam-library checks: [investigation log](investigation-log.md).

<h3 style="text-align:center">Network activity</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown.** No packet capture or network inspection was performed.

<h3 style="text-align:center">Permissions and persistence</h3>

User-scoped `Run` key (not machine-wide). Whether these values function as launch command lines or as stored identifiers was not established.

See [Permissions and persistence](../../concepts/permissions-and-persistence.md).

<h3 style="text-align:center">Privacy and telemetry</h3>

See [Privacy and telemetry](privacy-and-telemetry.md). Community association with AppsFlyer remains unverified for this installation.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Security considerations</h2>

| Finding | Label |
| --- | --- |
| Defender Quick scan reported no threats | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Scan result only — not proof of safety |
| `4126040` pair may relate to Aniimo + analytics SDK | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** |
| Aniimo is malware | **Not claimed** |
| Aniimo is definitively harmless | **Not claimed** |

See [Threat assessment](../../concepts/threat-assessment.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Diagnostic commands</h2>

Case-specific commands and observed results: [investigation log — Commands used](investigation-log.md#commands-used).

Reusable references:

- [Startup items](../../commands/windows/startup-items.md)
- [Registry](../../commands/windows/registry.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Findings and unresolved questions</h2>

<h3 style="text-align:center">Established observations</h3>

- Four unfamiliar values exist in the current user’s `Run` key (GUID-style data + counters `48` / `0`).
- Entries were reported disabled in Startup Apps.
- Steam metadata included Aniimo (`4126040`); user confirmed play via Steam.
- Defender Quick scan: no threats reported.
- No binary, network, or forensic deep-dive was performed.

<h3 style="text-align:center">❓ Unresolved questions</h3>

- Which component created each value?
- What does the associated software do with them?
- Do the entries trigger executable behaviour?
- What is the origin of `759396`?
- Is information transmitted to a third party?

<h3 style="text-align:center">Final assessment</h3>

> **❓ Unverified**
>
> Available evidence does **not** establish that Aniimo is malware, and does **not** independently establish exact origin or behaviour. The investigation remains incomplete.

Detail: [investigation log — conclusions](investigation-log.md#interpretation-and-current-conclusions).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- User observations and PowerShell / CIM queries in the [investigation log](investigation-log.md)
- Steam library metadata observation (`4126040` present; `759396` absent from reported installed list)
- Community reports linking `AF_uuid_*` / `AF_counter_*` patterns to AppsFlyer — **not** verified here
- [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)
- Microsoft `Run` key behaviour — see [startup items](../../commands/windows/startup-items.md) sources

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [Investigation log](investigation-log.md)
- [Evidence and confidence](../../methodology/evidence-and-confidence.md)
