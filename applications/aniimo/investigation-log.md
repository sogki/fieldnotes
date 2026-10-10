<h1 style="text-align:center">🕵️ Aniimo — investigation log</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Aniimo](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Authoritative chronology for the developing Aniimo / `AF_*` startup registry case study. Other Aniimo pages summarise findings and link here for command detail.

> **ℹ️ Publication note**
>
> Personal identifiers are redacted. GUID values appear as `[REDACTED GUID]`. Steam library paths use placeholders. Unrelated startup applications are omitted.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Summary</h2>

| | |
| --- | --- |
| **Status** | Incomplete investigation — not a security audit |
| **Focus** | Four `AF_uuid_*` / `AF_counter_*` values under the current-user `Run` key |
| **Strongest link** | Suffix `4126040` matches Aniimo’s Steam app ID; user installed/played Aniimo |
| **Open lead** | Community AppsFlyer naming association — <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible**, unverified here |
| **Assessment** | Evidence does **not** establish malware, and does **not** fully establish origin or behaviour |

<h3 style="text-align:center">Established observations</h3>

- Four unfamiliar registry values exist in the current user’s `Run` key.
- Stored values are GUID-style identifiers and numeric counters (`48`, `0`).
- Entries were reported as disabled in Windows Startup Apps / Task Manager.
- Steam library metadata included Aniimo (`4126040`); user confirmed Steam install/play.
- Microsoft Defender Quick scan reported no threats.
- No executable analysis, packet capture, network inspection, or independent forensic analysis was performed.

<h3 style="text-align:center">❓ Unresolved questions</h3>

- Which component created each registry value?
- What does the associated software actually do?
- Does either entry trigger executable behaviour?
- What is the origin of the `759396` pair?
- Is any information transmitted to a third party?

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Chronology](#chronology)
- [Commands used](#commands-used)
- [Interpretation and current conclusions](#interpretation-and-current-conclusions)
- [Follow-up research (not yet performed)](#follow-up-research-not-yet-performed)
- [Explicit non-claims](#explicit-non-claims)
- [Related resources](#related-resources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Chronology</h2>

<h3 style="text-align:center">1. Unfamiliar Startup Apps entries</h3>

**Question:** What are these Startup Apps entries, and do they look like normal autostart commands?

**Source:** User observation (Task Manager / Startup Apps UI)

**Observed:** Four unfamiliar entries, reported as **disabled**, with no identifiable publisher and no ordinary executable path shown in that UI.

**Supports:** Something registered unusual startup-related items at the user level.

**Still unknown:** Writer process, purpose, and whether the UI “disabled” state removed or only suppressed the entries.

**Next step:** Locate the underlying registry (or other) persistence location.

> **ℹ️ Note**
>
> Unfamiliar registry or startup entries do not, on their own, demonstrate malicious activity.

<h3 style="text-align:center">2. Location in the current-user `Run` key</h3>

**Question:** Where are the entries stored?

**Source:** Read-only registry inspection (see [commands B–C](#b-inspect-the-four-specific-run-values))

**Observed:** Values under:

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

| Value name | Sanitised observed data |
| --- | --- |
| `AF_uuid_4126040` | `[REDACTED GUID]` |
| `AF_counter_4126040` | `48` |
| `AF_uuid_759396` | `[REDACTED GUID]` |
| `AF_counter_759396` | `0` |

**Supports:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Presence and stored forms of the four values (GUIDs redacted for publication).

**Still unknown:** Who wrote them; whether Windows treats the data as launch strings.

<h3 style="text-align:center">3. Association with Aniimo via Steam app ID</h3>

**Question:** Does suffix `4126040` correspond to a known installed application?

**Source:** Steam app ID match + user confirmation + library metadata (see [command F](#f-inspect-steam-library-configuration))

**Observed:**

- Suffix `4126040` matches Aniimo’s Steam application ID.
- The user confirmed installing and playing Aniimo through Steam.
- Inspected Steam library configuration included application ID `4126040` (Aniimo).

**Supports:** <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Aniimo was present in the Steam environment under study.

**Still unknown:** Causal proof that Aniimo (or a shipped component) wrote the registry values.

<h3 style="text-align:center">4. Second identifier `759396`</h3>

**Question:** What application or catalogue ID is `759396`?

**Source:** Steam manifest search + library metadata (see [commands E–F](#e-check-installed-steam-game-manifests-for-759396))

**Observed:**

- Parallel `AF_*_759396` pair present.
- No matching `appmanifest_*.acf` for `759396` in the specific libraries checked.
- `759396` absent from the reported installed-app list in reviewed `libraryfolders.vdf` metadata.

**Supports:** Negative result for those checked locations only.

**Still unknown:** Origin of `759396` (uninstalled game, other library, or non-Steam software remain possible) — <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown**.

<h3 style="text-align:center">5. Community AppsFlyer association</h3>

**Question:** Do similar names appear in third-party discussions of analytics SDKs?

**Source:** Community reports (third-party claim)

**Observed:** Similar `AF_uuid_[APPID]` / `AF_counter_[APPID]` naming associated with AppsFlyer analytics / attribution in community reports.

**Supports:** <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** research lead only.

**Still unknown:** Whether AppsFlyer (or any SDK) is present in *this* installation — not verified by file or network analysis.

<h3 style="text-align:center">6. Microsoft Defender Quick scan</h3>

**Question:** Did a quick local malware scan report threats?

**Source:** User-reported Microsoft Defender Quick scan

**Observed:** No threats reported.

**Supports:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> That specific scan result at that time.

**Still unknown:** Broader security posture; Quick scan is not a full forensic analysis.

<h3 style="text-align:center">7. Work not performed</h3>

The following were **not** performed:

- Executable / binary analysis
- Packet capture or network inspection
- Independent forensic analysis beyond the commands recorded below

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Commands used</h2>

Reusable patterns: [startup items](../../commands/windows/startup-items.md) · [registry](../../commands/windows/registry.md).

All commands below are <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only** unless noted.

<h3 style="text-align:center">A. Enumerate Windows startup commands</h3>

**Question:** What startup commands does Windows expose via CIM?

```powershell
Get-CimInstance Win32_StartupCommand |
    Select-Object Name, Command, Location, User |
    Format-List
```

| | |
| --- | --- |
| **Observed** | Four entries used `AF_uuid_` / `AF_counter_` naming. Data were GUID-style identifiers and numeric counters, not ordinary executable paths |
| **Supports** | Unusual startup-visible entries exist |
| **Limitation** | Incomplete persistence inventory; no creator identification |

<h3 style="text-align:center">B. Inspect the four specific `Run` values</h3>

**Question:** What exact data is stored for the four names?

```powershell
$path = 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run'

Get-ItemProperty -Path $path |
    Select-Object AF_uuid_4126040, AF_counter_4126040,
                  AF_uuid_759396, AF_counter_759396 |
    Format-List
```

| | |
| --- | --- |
| **Observed** | Sanitised values in [section 2](#2-location-in-the-current-user-run-key) |
| **Supports** | Current value contents |
| **Limitation** | No history; no writer process |

<h3 style="text-align:center">C. Inspect all values in the startup registry key</h3>

**Question:** How do the `AF_` values sit among other `Run` entries?

```powershell
Get-ItemProperty `
    'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' |
    Format-List *
```

| | |
| --- | --- |
| **Observed** | Four `AF_` values appeared alongside recognisable applications. Unrelated entries and personal paths omitted from this public write-up |
| **Supports** | Coexistence with ordinary autostart entries |
| **Limitation** | Presence ≠ malice; these four did not resemble ordinary executable commands |

<details>
<summary>Optional: why full key dumps are not published</summary>

Publishing a complete `Run` key dump can expose personal install paths and unrelated application inventory. Fieldnotes keeps only the four investigated values and high-level notes about neighbouring recognisable entries.

</details>

<h3 style="text-align:center">D. Filter startup commands to the unfamiliar entries</h3>

**Question:** Can the four entries be isolated cleanly?

```powershell
Get-CimInstance Win32_StartupCommand |
    Where-Object { $_.Name -like 'AF_*' } |
    Format-List Name, Command, Location, User
```

| | |
| --- | --- |
| **Observed** | All four under the current user’s `Run` location; GUID-style values and counters `48` / `0` |
| **Limitation** | Same coverage limits as command A |

<h3 style="text-align:center">E. Check installed Steam game manifests for `759396`</h3>

**Question:** Is `759396` present as an installed Steam app in checked libraries?

> **ℹ️ Note**
>
> Replace `<STEAM_INSTALL_ROOT>` and `<ADDITIONAL_STEAM_LIBRARY>` with valid paths before running.

```powershell
$steamRoots = @(
    '<STEAM_INSTALL_ROOT>',
    '<ADDITIONAL_STEAM_LIBRARY>'
)

foreach ($root in $steamRoots) {
    $steamApps = Join-Path $root 'steamapps'

    if (Test-Path $steamApps) {
        Get-ChildItem $steamApps -Filter 'appmanifest_*.acf' `
            -ErrorAction SilentlyContinue |
            ForEach-Object {
                $file = Get-Content $_.FullName -Raw

                if ($file -match '"appid"\s+"759396"') {
                    Write-Output "Matching manifest found"
                }
            }
    }
}
```

| | |
| --- | --- |
| **Observed** | No matching manifest from the specific libraries checked |
| **Limitation** | Does not rule out uninstalled games, other libraries, or non-Steam apps |

<h3 style="text-align:center">F. Inspect Steam library configuration</h3>

**Question:** Which app IDs appear in Steam’s reported installed-app metadata?

Steam’s `libraryfolders.vdf` was inspected. Actual drive letters, library paths, content identifiers, and unrelated inventory are **not** published.

| | |
| --- | --- |
| **Observed** | `4126040` (Aniimo) present; `759396` absent from the reported installed-app list |
| **Limitation** | Absence ≠ malice; absence ≠ “never installed” |

<h3 style="text-align:center">G. Attempt to inspect the registry key’s last-write time</h3>

**Question:** When was the `Run` key last modified?

```powershell
Get-Item 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' |
    Select-Object Name, LastWriteTime
```

| | |
| --- | --- |
| **Observed** | Key name returned; `LastWriteTime` **not** populated |
| **Conclusion** | No usable creation/modification timestamp from this command. None is invented here |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Interpretation and current conclusions</h2>

<h3 style="text-align:center">Established observations</h3>

Listed in the [Summary](#summary) above.

<h3 style="text-align:center">Plausible but unverified interpretation</h3>

> **❓ Unverified**
>
> The `4126040` pair may be associated with Aniimo and an AppsFlyer-related analytics implementation, based on the matching Steam application ID and third-party community reports. This remains <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible**, not confirmed for this installation.

Unfamiliar registry entries and analytics-related identifiers do not, on their own, demonstrate malicious activity. See [Telemetry and analytics](../../concepts/telemetry-and-analytics.md).

<h3 style="text-align:center">Final assessment</h3>

The available evidence does **not** establish that Aniimo is malware. It also does **not** independently establish the exact origin or behaviour of the entries. The investigation remains incomplete.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Follow-up research (not yet performed)</h2>

These methods are possible next steps. They were **not** executed while preparing this documentation, and no results are claimed:

- [ ] Identify the application associated with identifier `759396` using reliable external sources
- [ ] Examine relevant application installation files and digital signatures
- [ ] Correlate registry changes with available installation records or timestamps
- [ ] Review official privacy policies and documentation for relevant analytics providers
- [ ] Where appropriate, perform controlled network analysis (redact; do not publish secrets)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Explicit non-claims</h2>

- Aniimo is **not** labelled malware.
- Aniimo is **not** certified harmless.
- AppsFlyer involvement is **not** independently verified for this installation.
- A clean Defender Quick scan is **not** a completed security audit.
- Negative Steam-manifest checks for `759396` are **not** conclusive exclusion of a Steam-related origin.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Aniimo overview](README.md)
- [Technical overview](technical-overview.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [Startup items](../../commands/windows/startup-items.md)
- [Registry](../../commands/windows/registry.md)
- [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)
- [Evidence and confidence](../../methodology/evidence-and-confidence.md)
