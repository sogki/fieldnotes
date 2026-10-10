<h1 style="text-align:center">🕵️ VALORANT & Riot Vanguard — investigation log</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Structured log for the developing VALORANT / Riot Vanguard case study. Companion pages summarise; this file records research chronology, sanitised local results, and follow-up that still needs explicit user action.

> **ℹ️ Publication note**
>
> No Windows username, computer name, personal paths, or raw system inventory is published here. Local paths appear only as placeholders such as `<VANGUARD_INSTALL>\vgc.exe`.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Summary</h2>

| | |
| --- | --- |
| **Status** | Developing case study — official docs + limited read-only local CIM inventory |
| **Focus** | Public architecture, Pre-Check / on-demand model, `vgc` / `vgk` registration state |
| **Strongest evidence class** | Official Riot + Microsoft documentation; local CIM rows for `vgc` / `vgk` |
| **Local snapshot** | Both `vgc` and `vgk` **Stopped**, **Manual** — **Observed locally — explanation unverified** |
| **Assessment** | Privileged anti-cheat is a documented trust tradeoff; Stopped/Manual is not treated as suspicious; Pre-Check activation is **not** confirmed |

<h3 style="text-align:center">Research already performed</h3>

- Verified reachability of primary Riot URLs (including re-verification of Pre-Check / on-demand pages on 2026-10-10)
- Documented Pre-Check / on-demand behaviour from official sources in [technical overview](technical-overview.md#vanguard-pre-check-and-on-demand-operation)
- Recorded sanitised read-only `Win32_Service` / `Win32_SystemDriver` results for `vgc` and `vgk`

<h3 style="text-align:center">Not performed</h3>

- No Fieldnotes agent re-execution of diagnostics on the user’s PC for this documentation update
- Signature / file-version inspection of `vgk.sys`
- VGTray Pre-Check eligibility confirmation
- Secure Boot / TPM / VBS status capture
- Launching VALORANT or forcing Vanguard to start
- Any configuration changes, service stops initiated by the investigation, or security-feature toggles

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Chronology of research performed](#chronology-of-research-performed)
- [Commands used for local observations](#commands-used-for-local-observations)
- [Interpretation and current conclusions](#interpretation-and-current-conclusions)
- [Follow-up research (not completed)](#follow-up-research-not-completed)
- [Explicit non-claims](#explicit-non-claims)
- [Related resources](#related-resources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Chronology of research performed</h2>

<h3 style="text-align:center">1. What is Riot Vanguard according to VALORANT Support?</h3>

**Question:** How does Riot publicly define Vanguard and its major pieces?

**Source:** [What is Vanguard?](https://support-valorant.riotgames.com/hc/en-us/articles/360046160933-What-is-Riot-Vanguard) (re-verified 2026-10-10)

**Observation:** Riot describes a client that runs while games are active and a kernel-mode driver; VGTray shows status and Pre-Check eligibility; Pre-Check enables on-demand start/stop without a full restart; usual model is on-boot.

**Interpretation:** High-level product model is <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented.

**Evidence confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">

**Limitations:** Support pages omit detection internals.

**Outstanding questions:** Install-specific Pre-Check activation.

**Suggested next step:** Optional VGTray read-only check (user-driven).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h3 style="text-align:center">2. What architecture did Riot publish in 2020?</h3>

**Question:** What components exist, and what privacy claims did Riot attach to them?

**Source:** [Security & Privacy teams message](https://www.riotgames.com/en/news/a-message-about-vanguard-from-our-security-privacy-teams) (2020-04-17)

**Observation:** Client / driver / platform model; driver start-up rationale; privacy and signing statements; bug-bounty scope announcement.

**Interpretation:** Primary source for the three-component model; later Pre-Check materials extend startup behaviour.

**Evidence confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for vendor statements; <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> for independent telemetry verification

**Limitations:** Predates on-demand mode.

**Outstanding questions:** Current driver network behaviour vs 2020 claims.

**Suggested next step:** None required for this update.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h3 style="text-align:center">3. How does on-demand / Pre-Check change startup behaviour?</h3>

**Question:** What does Riot document about on-demand operation and eligibility?

**Source:** [Incoming: Vanguard On-Demand](https://www.riotgames.com/en/news/vanguard-on-demand) (2026-06-24; re-verified 2026-10-10)

**Observation:** On-demand means the driver component no longer launches at system start on sufficiently secured PCs; Vanguard can launch when the game does and remain running only while playing a Riot title; Pre-Check is optional; documented requirements include Windows 11 25H2+, Secure Boot, TPM 2.0, VBS/HVCI, IOMMU; VGTray guides missing settings.

**Interpretation:** Startup behaviour is **mode-dependent**. Full write-up: [technical overview — Pre-Check](technical-overview.md#vanguard-pre-check-and-on-demand-operation).

**Evidence confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented product behaviour

**Limitations:** Does not prove the investigated PC uses this mode.

**Outstanding questions:** Local Pre-Check enablement and eligibility.

**Suggested next step:** VGTray status (read-only) if the user chooses.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h3 style="text-align:center">4. What did Riot publish about motherboard / pre-boot DMA issues?</h3>

**Question:** Why might VALORANT show security restrictions unrelated to “cheating”?

**Source:** [Vanguard Security Update: Closing the Pre-Boot Gap](https://www.riotgames.com/en/news/vanguard-security-update-motherboard) (2025-12-18)

**Observation:** Pre-boot DMA / IOMMU firmware signalling issues; VAN:Restriction flows; manufacturer advisories.

**Interpretation:** Platform integrity is part of Vanguard’s trust story.

**Evidence confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official Riot article

**Limitations:** No local firmware inventory.

**Outstanding questions:** Board/firmware revision on any given PC.

**Suggested next step:** OEM + Riot Support only if restricted—not investigative bypasses.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h3 style="text-align:center">5. What does the current Privacy Notice say about anti-cheat?</h3>

**Question:** How does Riot’s legal privacy disclosure describe anti-cheat?

**Source:** [Privacy Notice](https://www.riotgames.com/en/privacy-notice) (last modified 2026-04-28)

**Observation:** Fair-play processing; anti-cheat technologies that may run in the background; automated decisions possible.

**Interpretation:** Legal disclosure of practices—not a telemetry schema.

**Evidence confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official Notice

**Limitations:** Broad Riot Services scope.

**Outstanding questions:** Mapping Notice categories → Vanguard messages (<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown">).

**Suggested next step:** None for this update.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h3 style="text-align:center">6. What Windows concepts are needed to evaluate the trust model?</h3>

**Question:** How should Secure Boot, TPM, VBS/Memory Integrity, services, and Authenticode be understood?

**Source:** Microsoft Learn pages linked from [technical overview](technical-overview.md) and [services and drivers](services-and-drivers.md)

**Observation:** Microsoft definitions for platform security features and CIM service/driver classes.

**Interpretation:** Use Microsoft for OS vocabulary; Riot for product requirements.

**Evidence confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for Microsoft definitions

**Limitations:** Local feature state still <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown">.

**Outstanding questions:** Pre-Check eligibility checklist on the investigated PC.

**Suggested next step:** Optional read-only status views (Command set F)—user-driven only.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h3 style="text-align:center">7. Earlier note on absent local inspection</h3>

**Question:** Had the host PC been inventoried when the case study was first drafted?

**Source:** N/A

**Observation:** Initial documentation pass recorded no local CIM results.

**Interpretation:** Superseded by §8 below once sanitised results were supplied.

**Evidence confidence:** Historical note only

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h3 style="text-align:center">8. Local read-only inventory of `vgc` and `vgk`</h3>

**Question:** What Vanguard-related service and driver registrations does Windows report, and in what state?

**Source / commands:** Read-only PowerShell CIM queries (user-supplied results; see [commands used](#commands-used-for-local-observations)). Detailed tables: [services and drivers — local observations](services-and-drivers.md#local-observations).

**Observation:**

| Component | Interface | Name | Display name | State | StartMode | Path (sanitised) |
| --- | --- | --- | --- | --- | --- | --- |
| User-mode service | `Win32_Service` | `vgc` | `vgc` | `Stopped` | `Manual` | `<VANGUARD_INSTALL>\vgc.exe` |
| Kernel-mode driver | `Win32_SystemDriver` | `vgk` | `vgk` | `Stopped` | `Manual` | `vgk.sys` (exact path redacted) |

Both components were stopped and configured for manual startup at the time of inspection. The kernel-driver row came from **`Win32_SystemDriver`**, not from treating the service inventory as a complete driver catalogue.

**Interpretation:**

| Layer | Statement | Label |
| --- | --- | --- |
| Observation | `vgc` and `vgk` registered; Stopped; Manual; Vanguard paths reported | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed |
| Inference | Consistent with on-demand / Pre-Check idle behaviour | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** — **Observed locally — explanation unverified** |
| Not established | Pre-Check enabled; full eligibility; reason stopped; in-session load behaviour | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |

**Evidence confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for the CIM rows; <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> for the on-demand explanation

**Limitations:**

- Snapshot at one moment; no game session correlation
- Did not prove which product mode is active
- Did not inspect Authenticode status or file versions
- Read-only queries do not guarantee invisibility to anti-cheat software

**Outstanding questions:** Pre-Check activation; eligibility checklist; behaviour while VALORANT is running.

**Suggested next step:** Review Riot Pre-Check guidance; optional read-only VGTray check; record further sanitised snapshots with timestamps before revising the interpretation.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Commands used for local observations</h2>

**Risk:** <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> Read-only  
**Configuration changed?** No

<h3 style="text-align:center">A. User-mode service inventory (`vgc`)</h3>

```powershell
Get-CimInstance Win32_Service |
    Where-Object {
        $_.Name -in @('vgc', 'vgk') -or
        $_.DisplayName -match 'Riot Vanguard'
    } |
    Select-Object Name, DisplayName, State, StartMode, PathName
```

| | |
| --- | --- |
| **Produced** | `vgc` row (Stopped / Manual / `vgc.exe` path) |
| **Does not fully inventory** | Kernel drivers such as `vgk` |

<h3 style="text-align:center">B. Kernel-mode driver inventory (`vgk`)</h3>

```powershell
Get-CimInstance Win32_SystemDriver |
    Where-Object {
        $_.Name -match 'vgk' -or
        $_.DisplayName -match 'Riot Vanguard'
    } |
    Select-Object Name, DisplayName, State, StartMode, PathName
```

| | |
| --- | --- |
| **Produced** | `vgk` row (Stopped / Manual / `vgk.sys` path) |
| **Does not describe** | The user-mode `vgc` service by itself |

Full parameter notes: [services and drivers](services-and-drivers.md).

| Other templates | Status for this update |
| --- | --- |
| C (`sc.exe qc/query`) | Not required for recorded results |
| D–E (signature / file metadata) | Not performed |
| F (Secure Boot / TPM / VBS views) | Not performed |

> **⚠️ Caution**
>
> Do not use `sc stop`, `sc config`, debugger attachment, or firmware changes as “next steps.”

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Interpretation and current conclusions</h2>

<h3 style="text-align:center">Established</h3>

- VALORANT (PC) depends on Riot Vanguard for Riot’s competitive-integrity model (official docs).
- Pre-Check / on-demand is an officially documented optional model with published eligibility requirements.
- On this PC (sanitised inventory): `vgc` and `vgk` were registered, **Stopped**, and **Manual**.
- Kernel privilege expands system trust requirements regardless of start mode.

<h3 style="text-align:center">Plausible but unverified</h3>

- Stopped + Manual could reflect idle on-demand operation after Pre-Check activation — <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** only.

<h3 style="text-align:center">Final assessment (current)</h3>

> **Observed locally — explanation unverified**
>
> The CIM snapshot supports presence and idle Manual configuration of Vanguard’s service and driver registrations. It does **not** confirm Pre-Check, does **not** prove expected in-game behaviour, and does **not** support a malware verdict. The investigation remains developing.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Follow-up research (not completed)</h2>

Documented as potential next steps only—**not** executed by Fieldnotes for this update:

1. [ ] Review Riot’s official Pre-Check guidance (on-demand article + What is Vanguard?)
2. [ ] If the user chooses, inspect VGTray for eligibility / activation **without changing settings**
3. [ ] Record any further observations with timestamps and sanitised command output
4. [ ] Reassess the on-demand interpretation only when additional evidence is available
5. [ ] Optional: signature / version metadata for `vgk.sys` (Commands D–E)
6. [ ] Optional: read-only Secure Boot / TPM / Memory Integrity views (Command F)

Still **out of scope:** memory inspection, injection, disabling Vanguard, firmware experiments, cheat tooling, or installing diagnostic kernel drivers.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Explicit non-claims</h2>

- Pre-Check / on-demand is **not** confirmed enabled on the investigated PC.
- Stopped components are **not** labelled suspicious solely because they were stopped.
- Vanguard is **not** labelled malware.
- Vanguard is **not** certified harmless or vulnerability-free.
- Riot’s privacy statements are **not** treated as independently proven.
- Fieldnotes did **not** execute diagnostic commands on the user’s PC while applying this documentation update.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Case overview](README.md)
- [Technical overview — Pre-Check](technical-overview.md#vanguard-pre-check-and-on-demand-operation)
- [Services and drivers — local observations](services-and-drivers.md#local-observations)
- [Privacy and security](privacy-and-security.md)
- [Aniimo investigation log](../aniimo/investigation-log.md) (separate case; evidence-style reference)
- [Evidence and confidence](../../methodology/evidence-and-confidence.md)
