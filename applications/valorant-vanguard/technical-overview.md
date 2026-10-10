<h1 style="text-align:center">VALORANT & Riot Vanguard — technical overview</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Publicly documented technical context for VALORANT and Riot Vanguard. Sanitised local service/driver observations live in [services and drivers](services-and-drivers.md#local-observations) and the [investigation log](investigation-log.md#8-local-read-only-inventory-of-vgc-and-vgk).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Developer and publisher](#developer-and-publisher)
- [Official distribution channels](#official-distribution-channels)
- [Roles: VALORANT vs Vanguard](#roles-valorant-vs-vanguard)
- [Stated purpose](#stated-purpose)
- [Publicly documented architecture](#publicly-documented-architecture)
- [User-mode vs kernel-mode](#user-mode-vs-kernel-mode)
- [Vanguard Pre-Check and on-demand operation](#vanguard-pre-check-and-on-demand-operation)
- [Startup models compared](#startup-models-compared)
- [Windows security requirements](#windows-security-requirements)
- [Limitations of public information](#limitations-of-public-information)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Developer and publisher</h2>

| Item | Detail | Confidence |
| --- | --- | --- |
| Developer / publisher | Riot Games | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |
| Game | VALORANT | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |
| Anti-cheat product | Riot Vanguard | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |
| Development model | Riot states Vanguard was developed in-house | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Vendor statement |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Official distribution channels</h2>

| Channel | Notes | Confidence |
| --- | --- | --- |
| Riot Client / game install | Vanguard installs with games that require it (VALORANT is a primary example); Riot support states reinstall occurs when launching a requiring game | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official support wording |
| Windows Apps & Features | Riot documents uninstall via “Riot Vanguard” in Add/Remove Programs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |

Other third-party redistributors are outside this case study’s trust model. Prefer Riot’s own client and support guidance.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Roles: VALORANT vs Vanguard</h2>

| Component | Role (public description) | Confidence |
| --- | --- | --- |
| **VALORANT** | Competitive multiplayer game client and online service | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| **Riot Vanguard** | Riot’s game security / anti-cheat software required while playing most Riot titles that opt into it | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official support |

Riot’s VALORANT support article states that Vanguard must remain active while playing most Riot games that use it, and that unexpected interactions with the client or driver may affect playability. Uninstalling Vanguard prevents those games from running until it is present again.

> **ℹ️ Note**
>
> Vanguard is a **shared** Riot anti-cheat product, not a VALORANT-only binary in Riot’s public description. League of Legends and other titles may also require it. This case study centres on VALORANT as the motivating application.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Stated purpose</h2>

Riot describes Vanguard as custom game security software intended to uphold competitive integrity. The 2020 Security & Privacy teams article frames the problem as an arms race: cheats moved beyond user-mode memory manipulation toward techniques that attempt to modify or sit beneath the operating system (including DMA-style approaches). Riot’s position is that a purely user-mode anti-cheat can be defeated by higher-privilege cheats.

That purpose statement is **officially documented**. Whether any specific detection technique is effective in practice is outside the evidence collected here.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Publicly documented architecture</h2>

Riot’s 2020 architecture summary describes **three** components:

| Component | Mode / place | Documented responsibilities |
| --- | --- | --- |
| **Client** | User-mode | Handles anti-cheat detections while a game is running; communicates with Riot’s platform; treats the machine as untrusted unless it recognises the driver |
| **Driver** | Kernel-mode | Validates memory and system state; helps ensure the client has not been tampered with; historically associated with start-up load so cheats cannot load before client initialisation |
| **Platform** | Riot-side service | Receives / supplies detection-related communication so play can proceed |

Additional UI component described in later support / engineering posts:

| Component | Notes | Confidence |
| --- | --- | --- |
| **VGTray** (system tray) | Status visibility, security-requirement guidance, Pre-Check / on-demand controls | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official support + on-demand article |

<h3 style="text-align:center">Common Windows identifiers (names)</h3>

| Identifier | Typical association | Confidence for this case study |
| --- | --- | --- |
| `vgc` | User-mode Vanguard Windows service | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed as a registered `Win32_Service` (Stopped / Manual at inspection) |
| `vgk` / `vgk.sys` | Kernel-mode driver registration / image | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Locally observed via `Win32_SystemDriver` (Stopped / Manual at inspection); path sanitised in public notes |

Details and commands: [services and drivers](services-and-drivers.md#local-observations).

Do **not** speculate about undisclosed internal detection algorithms, undocumented IOCTLs, or unpublished blocklists. Riot explicitly limits how far it will discuss internals without compromising anti-cheat effectiveness.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">User-mode vs kernel-mode</h2>

| Mode | Rough meaning | Relevance to Vanguard |
| --- | --- | --- |
| **User-mode** | Ordinary application / service privilege. Faults are usually contained to the process. Access to other processes and hardware is mediated by the OS. | Documented Vanguard **client** runs here and performs detections while games are active |
| **Kernel-mode** | Highest privilege ring used by the Windows kernel and drivers. Bugs or abuse can affect system-wide stability and security. | Documented Vanguard **driver** runs here to validate memory / system state and protect the client’s integrity assumptions |

> **ℹ️ Note**
>
> Kernel-mode is a **privilege class**, not a moral category. Antivirus, disk encryption, virtualisation, and anti-cheat products may all ship drivers. The security question is what trust you place in the publisher, update channel, signing, vulnerability response, and observed behaviour—not merely that a `.sys` file exists.

Background: [Permissions and persistence](../../concepts/permissions-and-persistence.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Vanguard Pre-Check and on-demand operation</h2>

Primary sources (re-verified 2026-10-10):

- [Incoming: Vanguard On-Demand](https://www.riotgames.com/en/news/vanguard-on-demand) (2026-06-24)
- [What is Vanguard?](https://support-valorant.riotgames.com/hc/en-us/articles/360046160933-What-is-Riot-Vanguard)

<h3 style="text-align:center">What “on-demand” means (official)</h3>

Riot defines **on-demand** operation as a mode in which Vanguard’s **driver component does not launch when Windows starts**. Instead, when Pre-Check eligibility and activation allow it, Vanguard can **launch when a Riot game starts** and **remain running only while the player is playing a Riot title**.

VALORANT Support states that Pre-Check enables starting and stopping Vanguard on-demand **without a full restart**, in contrast to the usual on-boot model where disabling Vanguard and later wanting to play typically requires a restart.

| Topic | Official description | Confidence |
| --- | --- | --- |
| Boot behaviour (on-demand) | Driver component no longer launches at system start | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| Game start | Vanguard launches when the game does | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| During / after play | Remains running only while playing a Riot title; tray icon described as absent unless actively playing | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| Optionality | Pre-Check / on-demand is optional; players may leave the always-present model unchanged | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |

> **ℹ️ Note — local PC**
>
> These statements describe Riot’s published product model. They do **not** establish that Pre-Check is enabled on the machine under study. See [local observations](services-and-drivers.md#local-observations).

<h3 style="text-align:center">How Pre-Check differs from automatic boot start</h3>

| Model | Documented behaviour |
| --- | --- |
| **Usual / on-boot** | Support article: Vanguard is usually an on-boot application; after disable, playing again typically needs a restart. Engineering posts historically emphasise early load for the “who loads first?” integrity problem. |
| **Pre-Check on-demand** | Driver need not start with Windows; Vanguard associates with Riot-title play sessions; VGTray can guide eligibility and may vanish when not needed. |

Riot’s on-demand article ties the feasibility of leaving the driver off at boot to modern Windows/platform protections (including runtime driver attestation) that reduce reliance on continuous early presence.

<h3 style="text-align:center">Documented Pre-Check eligibility requirements</h3>

From Riot’s on-demand article (paraphrased; confirm the live page before acting on a personal PC):

| Requirement | Official article wording (summary) | Confidence |
| --- | --- | --- |
| Windows version | At least **Windows 11 25H2** for Pre-Check (runtime driver attestation called out as a reason) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| Firmware / boot | **UEFI** mode and **Secure Boot** enabled | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| TPM | **TPM 2.0** (discrete or firmware TPM sufficient for Pre-Check per article; restriction scenarios may differ) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| Virtualisation protections | **VBS** and **HVCI** (Memory Integrity) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |
| DMA protections | **IOMMU** enabled | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented |

> **⚠️ Caution**
>
> Documented here for understanding only. Do **not** change firmware, Secure Boot, TPM, VBS, HVCI, or IOMMU settings as part of this investigation.

<h3 style="text-align:center">Checking eligibility or activation status</h3>

| Interface | What Riot documents | Use in this investigation |
| --- | --- | --- |
| **VGTray** (Vanguard system tray icon) | Support: shows status and whether the PC meets requirements for Vanguard or Pre-Check; can start/stop on-demand without a full restart when eligible. On-demand article: redesigned VGTray helps identify missing settings. | Optional **read-only** look by the user — do not change settings |
| Riot player support / on-demand article | Guidance for enabling on-demand when desired | Prefer reading over experimenting |

Windows-native Secure Boot / TPM / Memory Integrity **views** (without changing them) are listed in [services and drivers — F](services-and-drivers.md#f-windows-security-status-checks). Those checks alone still do **not** prove Pre-Check activation.

<h3 style="text-align:center">Motherboard / pre-boot enforcement context</h3>

Riot’s December 2025 security update describes motherboard firmware issues around pre-boot DMA protection / IOMMU initialisation, manufacturer advisories, and **VAN:Restriction** prompts. That is a **compatibility and enforcement** topic, not proof of user wrongdoing.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Startup models compared</h2>

| Behaviour | Source type | Confidence |
| --- | --- | --- |
| Driver historically associated with system start-up | Official Riot architecture (2020) + later engineering articles | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> / <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> |
| Restart often required after install or re-enable (on-boot model) | Official support wording | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Optional on-demand when Pre-Check requirements are met | Official on-demand + support articles | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Uninstall via Apps & Features; requiring games will not run without Vanguard | Official support | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Local `vgc` / `vgk` Stopped + Manual ⇒ Pre-Check confirmed | Inference only | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible**, not established |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Windows security requirements</h2>

| Technology | Why it appears in Vanguard / Pre-Check docs | Microsoft concept page |
| --- | --- | --- |
| **Secure Boot** | Pre-Check requirement; bootkit / pre-OS cheat pressure | <a href="https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-secure-boot-key-creation-and-management-guidance" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Secure Boot guidance</a> |
| **TPM 2.0** | Pre-Check requirement; attestation / hardware identity context in Riot’s article | Platform security / TPM docs on Microsoft Learn |
| **VBS + HVCI (Memory Integrity)** | Pre-Check requirement; hardens kernel code integrity | [Memory integrity and VBS](https://learn.microsoft.com/en-us/windows-hardware/drivers/bringup/device-guard-and-credential-guard) · [Enable memory integrity](https://learn.microsoft.com/en-us/windows/security/hardware-security/enable-virtualization-based-protection-of-code-integrity) |
| **IOMMU** | Pre-Check requirement; DMA cheat mitigation context | Discussed in Riot engineering posts; firmware / OS features |
| **Driver signing** | Production kernel drivers are expected to be signed; Riot states EV signing under Microsoft’s process | See [digital signatures](../../concepts/digital-signatures.md) |

Exact playability requirements for a given account, region, or rank can change. Prefer current Riot support articles over this static summary when troubleshooting.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Limitations of public information</h2>

| Topic | Status |
| --- | --- |
| Full detection logic and private heuristics | Intentionally unpublished |
| Complete file inventory for every release | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not established here |
| Independent, continuous audit of telemetry contents | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not established here |
| Whether Pre-Check is enabled on the investigated PC | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** — see local CIM limits |
| In-session behaviour while VALORANT is running | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not observed |
| Vendor security reviews / bug bounty existence | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Riot states internal + external review and HackerOne scope — still not a public full audit report in this repo |

> **❓ Unverified**
>
> Do not invent certificate subject names, Pre-Check activation state, or in-game driver load behaviour without supporting evidence.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

| Source | Use | Accessed |
| --- | --- | --- |
| <a href="https://support-valorant.riotgames.com/hc/en-us/articles/360046160933-What-is-Riot-Vanguard" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> What is Vanguard?</a> | High-level product description, tray / Pre-Check, on-demand without full restart | 2026-10-10 (re-verified) |
| <a href="https://www.riotgames.com/en/news/a-message-about-vanguard-from-our-security-privacy-teams" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Security & Privacy teams message</a> | Client / driver / platform architecture, privacy philosophy (2020) | 2026-10-10 |
| <a href="https://www.riotgames.com/en/news/vanguard-on-demand" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Vanguard On-Demand</a> | Pre-Check definition, session lifecycle, eligibility requirements | 2026-10-10 (re-verified) |
| <a href="https://www.riotgames.com/en/news/vanguard-security-update-motherboard" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Motherboard pre-boot security update</a> | IOMMU / VAN:Restriction context | 2026-10-10 |
| Microsoft Learn — Memory integrity / VBS / Secure Boot pages linked above | OS security definitions | 2026-10-10 |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Case overview](README.md)
- [Services and drivers](services-and-drivers.md)
- [Privacy and security](privacy-and-security.md)
- [Investigation log](investigation-log.md)
