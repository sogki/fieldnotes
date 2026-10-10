<h1 style="text-align:center"><img src="../../assets/branding/icon-game.svg" alt="" width="40" height="40" style="vertical-align:middle;margin-right:10px">VALORANT & Riot Vanguard</h1>

<a href="../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Applications</a> · <a href="../../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes</a>

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">


Riot Games’ competitive shooter **VALORANT** depends on **Riot Vanguard**, Riot’s in-house anti-cheat stack that includes a user-mode client and a kernel-mode driver. This case study documents publicly described architecture, Windows integration patterns, privacy disclosures, and safe read-only inspection methods—without attempting to prove Vanguard is malicious or harmless.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Overview</h2>

| Field | Value | Confidence |
| --- | --- | --- |
| Game in scope | VALORANT (PC) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official product |
| Anti-cheat in scope | Riot Vanguard | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official product |
| Developer / publisher | Riot Games | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |
| Privileged component | Kernel-mode driver (documented) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| Local installation inspected for this write-up | Partial — read-only `vgc` / `vgk` CIM queries (user-supplied) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed state; paths sanitised |
| Vanguard Pre-Check / on-demand enabled on the PC | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** | Not established by service/driver state alone |
| Malware determination | None made | Explicitly **not** claimed |

> **🕵️ Investigation status**
>
> Developing case study — grounded in official Riot and Microsoft documentation, plus a limited read-only Windows inventory of `vgc` and `vgk`. Both components were **Stopped** with **Manual** start mode at inspection time. That pattern is **Observed locally — explanation unverified**; it may be consistent with on-demand operation but does **not** prove Pre-Check is enabled. Privileged access remains a **trust and risk surface**, not evidence of malice.

<h3 style="text-align:center">On this page</h3>

- [Purpose and scope](#purpose-and-scope)
- [Table of contents](#table-of-contents)
- [Brief assessment](#brief-assessment)
- [Safety boundaries](#safety-boundaries)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Purpose and scope</h2>

**Purpose:** Build a cybersecurity-learning case study that explains how VALORANT and Vanguard relate, what Riot publishes about architecture and privacy, how Windows services/drivers and signatures fit the picture, and what remains unverified.

**In scope**

- Official Riot disclosures about Vanguard’s roles and components
- Distinction between user-mode and kernel-mode software
- Documented Windows security expectations (Secure Boot, TPM, VBS / Memory Integrity, and related Pre-Check features)
- Safe, voluntary, read-only Windows diagnostic command examples
- Evidence classification and open questions

**Out of scope**

- Debugging, memory inspection, injection, or anti-cheat evasion
- Stopping, disabling, uninstalling, or reconfiguring Vanguard
- Changing Secure Boot, TPM, VBS, HVCI, driver-signing, or firmware settings
- Installing diagnostic utilities or kernel drivers for this investigation
- Claiming a completed independent security audit

Cross-link, do not duplicate: [Aniimo case study](../aniimo/README.md) for Fieldnotes evidence style and user-scoped persistence examples.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

| Document | Role |
| --- | --- |
| <a href="technical-overview.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Technical overview</a> | Roles, architecture, Pre-Check / on-demand model, Windows requirements |
| <a href="services-and-drivers.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Services and drivers</a> | Local `vgc` / `vgk` observations, safe inspection methods, interpretation |
| <a href="privacy-and-security.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Privacy and security</a> | Disclosures, kernel privilege implications, least privilege, independent research limits |
| <a href="investigation-log.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log</a> | Chronology, local CIM results, follow-up that still needs user action |

Reusable Fieldnotes material:

- [Services (Windows)](../../commands/windows/services.md)
- [Digital signatures](../../concepts/digital-signatures.md)
- [Permissions and persistence](../../concepts/permissions-and-persistence.md)
- [Threat assessment](../../concepts/threat-assessment.md)
- [Evidence and confidence](../../methodology/evidence-and-confidence.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Brief assessment</h2>

| Claim | Label |
| --- | --- |
| VALORANT relies on Riot Vanguard for competitive integrity on PC | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| Vanguard includes a user-mode client and a kernel-mode driver | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| Kernel-mode code is highly privileged and expands the trust surface | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> General OS security principle + vendor acknowledgment of concern |
| A valid publisher signature proves software is vulnerability-free or non-abusive | **False / unsupported** — see [digital signatures](../../concepts/digital-signatures.md) |
| Kernel privilege alone proves malware | **Not claimed** |
| Vanguard has been independently proven free of privacy or security defects | **Not claimed** — vendor statements ≠ independent audit of every behaviour |
| `vgc` and `vgk` were registered, Stopped, Manual at one inspection | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed (see [services and drivers](services-and-drivers.md#local-observations)) |
| Stopped + Manual proves Pre-Check / on-demand is active | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** only — **not established** |
| Stopped components are suspicious by themselves | **Not claimed** |

> **🔍 Finding**
>
> Privileged anti-cheat software requires a deliberate trust decision: the vendor’s kernel component can observe and affect the system more deeply than ordinary apps. That is a **capability and trust** finding. It is not, by itself, evidence of malicious behaviour. A stopped Manual `vgc` / `vgk` pair is likewise not, by itself, evidence of compromise or of confirmed on-demand mode.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Safety boundaries</h2>

> **⚠️ Caution**
>
> Do **not** attach debuggers to VALORANT or Vanguard, read game memory, inject code, intercept anti-cheat traffic, modify Vanguard binaries or services, stop or disable Vanguard, change firmware / Secure Boot / TPM / VBS settings for this investigation, or test cheat-detection evasion.
>
> Commands in this case study are **examples for voluntary, read-only use**. Fieldnotes agents and contributors must not auto-execute them against a live anti-cheat install. Read-only queries are lower risk than state changes, but they are **not** guaranteed invisible to anti-cheat software or free of false-positive risk.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

Primary starting points (verified reachable; wording may change):

- [What is Vanguard?](https://support-valorant.riotgames.com/hc/en-us/articles/360046160933-What-is-Riot-Vanguard) — VALORANT Support (verified 2026-10-10)
- [VALORANT Support](https://support-valorant.riotgames.com/hc/en-us) (verified 2026-10-10)
- [A Message About Vanguard From Our Security & Privacy Teams](https://www.riotgames.com/en/news/a-message-about-vanguard-from-our-security-privacy-teams) (2020-04-17; accessed 2026-10-10)
- [Incoming: Vanguard On-Demand](https://www.riotgames.com/en/news/vanguard-on-demand) (2026-06-24; re-verified 2026-10-10)
- [Vanguard Security Update: Closing the Pre-Boot Gap](https://www.riotgames.com/en/news/vanguard-security-update-motherboard) (2025-12-18; accessed 2026-10-10)
- [Riot Games Privacy Notice](https://www.riotgames.com/en/privacy-notice) (last modified 2026-04-28; accessed 2026-10-10)

Microsoft Learn topics used for Windows concepts are cited in the companion documents.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Services and drivers](services-and-drivers.md)
- [Privacy and security](privacy-and-security.md)
- [Investigation log](investigation-log.md)
- [Aniimo overview](../aniimo/README.md) (separate case study)
