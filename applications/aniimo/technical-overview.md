<h1 style="text-align:center">Aniimo — technical overview</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Aniimo](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Technical context for the [Aniimo case study](README.md). Command transcripts and sanitised results live only in the [investigation log](investigation-log.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">🔎 Application identification</h2>

| Item | Detail | Confidence |
| --- | --- | --- |
| Steam application ID | `4126040` | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Matches `AF_*_4126040`; present in inspected Steam library metadata |
| User relationship | Installed and played via Steam | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> User report |
| Second ID | `759396` | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Present only as registry-value suffix so far |

Developer and publisher names are <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** here pending official metadata citation.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📦 Distribution and installation</h2>

<h3 style="text-align:center">Official distribution channels</h3>

Steam is the only distribution channel in scope for this case study. Other channels: <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown**.

<h3 style="text-align:center">Installation and update behaviour</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** in detail. Steam-managed install/update is presumed from the user’s Steam install/play confirmation, but no install-time or update-time tracing was recorded.

<h3 style="text-align:center">Files and directories</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown.** No install-tree listing, hash inventory, or digital-signature review was performed.

Privacy-safe Steam path examples (placeholders only):

```text
<STEAM_INSTALL_ROOT>\steamapps\
<ADDITIONAL_STEAM_LIBRARY>\steamapps\
```

Manifest and `libraryfolders.vdf` checks: [investigation log — commands E and F](investigation-log.md#e-check-installed-steam-game-manifests-for-759396).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">⚙️ Technical behaviour</h2>

<h3 style="text-align:center">Processes and services</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown.** Not inspected for this case study.

<h3 style="text-align:center">Registry entries and startup behaviour</h3>

**Key:**

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

**Values (public / sanitised):**

| Name | Data |
| --- | --- |
| `AF_uuid_4126040` | `[REDACTED GUID]` |
| `AF_counter_4126040` | `48` |
| `AF_uuid_759396` | `[REDACTED GUID]` |
| `AF_counter_759396` | `0` |

**Context:**

- Discovered first as unfamiliar, disabled Startup Apps entries (Task Manager) without ordinary executable paths or identifiable publishers in that UI.
- Confirmed under the current-user `Run` key via read-only PowerShell / CIM queries.
- Data did not resemble ordinary executable command lines.
- Attempt to read `LastWriteTime` on the `Run` key did not yield a usable timestamp.

> **❓ Unverified**
>
> Numeric suffixes look like application IDs; only `4126040` is tied to Aniimo with supporting Steam evidence in this case. AppsFlyer association is a <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> community lead, not a verified binary finding.

<h3 style="text-align:center">Network activity</h3>

<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown.** No capture or connection attribution was performed.

<h3 style="text-align:center">Permissions and persistence</h3>

| Item | Status |
| --- | --- |
| Scope | Current-user `Run` key |
| Machine-wide `Run` / services / tasks | Not assessed |
| Executable launch behaviour of these values | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |

Background: [Permissions and persistence](../../concepts/permissions-and-persistence.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">🔐 Security considerations</h2>

- Clean Microsoft Defender Quick scan: reported by the user; limited assurance.
- Do not equate analytics-like naming with malware.
- Do not treat incomplete Steam lookups for `759396` as proof of malice or innocence.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">⌨️ Diagnostic commands</h2>

Authoritative list: [investigation log](investigation-log.md#commands-used).

Reusable pages: [startup items](../../commands/windows/startup-items.md) · [registry](../../commands/windows/registry.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Aniimo overview](README.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [Investigation log](investigation-log.md)
- [Digital signatures](../../concepts/digital-signatures.md)
