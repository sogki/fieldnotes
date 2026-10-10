<h1 style="text-align:center">VALORANT & Riot Vanguard — privacy and security</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Separates **Riot’s published disclosures**, **general security principles**, and **what has not been independently verified**. Kernel privilege is treated as a trust surface—not as proof of malware.

Related concepts: [Telemetry and analytics](../../concepts/telemetry-and-analytics.md) · [Threat assessment](../../concepts/threat-assessment.md) · [Digital signatures](../../concepts/digital-signatures.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Riot’s published privacy disclosures](#riots-published-privacy-disclosures)
- [Documented telemetry practices and limits](#documented-telemetry-practices-and-limits)
- [Security implications of kernel-mode software](#security-implications-of-kernel-mode-software)
- [Trust, signing, and vulnerability management](#trust-signing-and-vulnerability-management)
- [Least privilege](#least-privilege)
- [Stability and compatibility](#stability-and-compatibility)
- [Independent security research](#independent-security-research)
- [Vendor statements vs independent verification](#vendor-statements-vs-independent-verification)
- [Assessment table](#assessment-table)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Riot’s published privacy disclosures</h2>

<h3 style="text-align:center">Privacy Notice (product-wide)</h3>

Riot’s [Privacy Notice](https://www.riotgames.com/en/privacy-notice) (last modified 2026-04-28; accessed 2026-10-10) describes categories of personal info that may be collected for Riot Services generally, including identifiers, device/network activity info, and commercial/gameplay-related data.

Relevant themes for this case study:

| Topic | What the Notice states (paraphrased) | Confidence |
| --- | --- | --- |
| Fair play / anti-cheat purpose | Detecting cheating and unauthorized behaviour is listed among player-related processing activities | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official Notice |
| Anti-cheat technologies | Riot may use anti-cheat and fraud-prevention technologies (including software that may run in the background) and may make automated decisions (for example suspensions or restricted access) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official Notice § Anti-Cheat |
| Background operation | The Notice explicitly contemplates anti-cheat software that may run in the background | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official wording |

> **ℹ️ Note**
>
> The Privacy Notice is a **legal disclosure of practices Riot may use**, not a packet-level inventory of every Vanguard field. It also covers many non-Vanguard services (chat, purchases, websites, events).

<h3 style="text-align:center">2020 Security & Privacy teams article (Vanguard-specific claims)</h3>

Riot’s [2020 message](https://www.riotgames.com/en/news/a-message-about-vanguard-from-our-security-privacy-teams) includes sharper Vanguard-specific statements, including (paraphrased):

- Vanguard was designed with Security and Data Privacy involvement.
- Riot states Vanguard does **not** collect or process personal information beyond what the then-current League of Legends anti-cheat solution did.
- Riot states the **driver does not collect or send** information about the computer back to Riot.
- Riot states the **client** communicates with Riot’s platform as part of enabling play / detections.
- Riot describes internal and external security review and an expanded HackerOne bounty scope for Vanguard issues.

| Claim class | Treatment |
| --- | --- |
| Vendor privacy / architecture statements | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Officially documented as Riot’s position |
| Independent proof that every build matches those statements | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not established in this repository |
| “Collects no information” as an absolute for all Riot Services | **Do not claim** — the Privacy Notice describes broader collection for Riot Services, and the 2020 article distinguishes driver vs client / platform roles |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Documented telemetry practices and limits</h2>

| Question | Current evidence-based answer |
| --- | --- |
| Does Riot claim *some* game/integrity-related data collection exists? | Yes — Privacy Notice and 2020 article discuss integrity-related services and client–platform communication |
| Does Riot claim the **kernel driver** itself phones home with PC inventory? | Riot’s 2020 article states the driver does not collect or send computer information | 
| Has Fieldnotes independently verified wire contents? | **No** — network capture was not performed |
| Can “no personal info beyond LoL anti-cheat” be audited from public docs alone? | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Insufficient — needs policy comparison over time + technical verification |

> **❓ Unverified**
>
> Do not claim Vanguard collects **no** information. Do not claim Fieldnotes has validated Riot’s telemetry inventory. Prefer: “Riot states X; independent confirmation of X is outside this case study.”

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Security implications of kernel-mode software</h2>

Kernel-mode components run with privileges that can:

- Observe or alter system-wide state beyond a single user process
- Affect stability (driver bugs → crashes / bugchecks)
- Expand impact if the component is compromised (vulnerability exploited by malware or cheat tooling)

Riot’s own communications acknowledge that shipping a kernel driver raises player concern and argue the privilege is motivated by cheat techniques that defeat user-mode-only defenses.

| Implication | Label |
| --- | --- |
| Kernel anti-cheat increases the amount of trust placed in the vendor and update pipeline | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Sound security reasoning |
| Kernel anti-cheat is therefore malware | **Not supported** — privilege ≠ malice |
| Kernel anti-cheat eliminates all cheat risk | **Not supported** — arms race continues (Riot’s own later articles discuss DMA, bootkits, etc.) |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Trust, signing, and vulnerability management</h2>

<h3 style="text-align:center">Driver signing</h3>

Riot states the Vanguard driver was signed with Riot’s EV certificate under Microsoft’s code-signing process. Signing supports **publisher attribution and integrity of the signed binary** when verification succeeds. It does **not** guarantee absence of bugs or contested design choices.

See [Digital signatures](../../concepts/digital-signatures.md).

<h3 style="text-align:center">Vulnerability management (vendor-stated)</h3>

| Mechanism | Riot statement | Independence |
| --- | --- | --- |
| Internal review | Claimed | Vendor |
| External security audits | Claimed | Vendor summary — full reports not reproduced here |
| Bug bounty (HackerOne), including Vanguard scope | Claimed / historically announced | Program existence is publicly referenced; bounty outcomes are not catalogued in this repo |

A bounty program is a **positive security practice signal**, not a certificate that no vulnerabilities exist.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Least privilege</h2>

The principle of **least privilege** says software should run with only the rights it needs. Kernel anti-cheat is a deliberate departure from least privilege for the anti-cheat component, justified by Riot as necessary against higher-privilege cheats.

| Perspective | Notes |
| --- | --- |
| Competitive integrity | Riot argues user-mode-only defenses are insufficient against modern cheat classes |
| System security / privacy | Users inherit a larger trusted computing base (TCB) on each machine that installs the driver |
| Optional mitigation path | Riot’s on-demand / Pre-Check model tries to lean on Windows platform security so continuous driver presence is less necessary on sufficiently hardened PCs |

Product detail: [technical overview — Pre-Check](technical-overview.md#vanguard-pre-check-and-on-demand-operation). Local `vgc` / `vgk` were observed Stopped / Manual; that does **not** by itself prove Pre-Check is active ([services and drivers](services-and-drivers.md#interpretation-observation-vs-inference)).

Fieldnotes does not adjudicate which tradeoff a player “should” accept; it documents that the tradeoff exists.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Stability and compatibility</h2>

Official and engineering communications note that:

- Unexpected interactions with the client or driver may affect play
- Security feature requirements (Secure Boot, TPM, VBS/HVCI, IOMMU, firmware updates) can block play via restrictions when baselines are not met
- Motherboard firmware issues have required manufacturer updates in documented cases

These are **compatibility and reliability** topics. They are not, by themselves, evidence of intentional harm.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Independent security research</h2>

| Kind of material | How this case study treats it |
| --- | --- |
| Riot official docs | Primary sources for vendor claims |
| Microsoft Learn | Primary sources for OS security definitions |
| Reputable third-party reverse-engineering or audit write-ups | May be cited later with clear **Third-party reported** labels — none are adopted as <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> facts here without review |
| Forum speculation / cheat-marketing blogs | Unreliable for portfolio conclusions |

No independent third-party audit report is reproduced or endorsed in this repository revision.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Vendor statements vs independent verification</h2>

| Claim | Vendor-documented? | Independently verified here? |
| --- | --- | --- |
| Vanguard has client + driver (+ platform) | Yes | Architecture description accepted as Riot’s public model; internals not reverse-engineered |
| Driver does not send PC info | Yes (2020 article) | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not verified by traffic analysis in this repo |
| External audits occurred | Yes (claimed) | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Audit reports not attached here |
| EV / Microsoft signing process used | Yes (claimed) | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Local signature inspection not performed in this write-up |
| Pre-Check security features reduce need for always-on driver | Yes (2026 on-demand article) | Local Stopped/Manual `vgc`/`vgk` is <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> consistent but Pre-Check activation remains <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Assessment table</h2>

| Finding | Label | Type |
| --- | --- | --- |
| Vanguard is privileged software by design | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> | Officially documented + OS principles |
| Privilege alone proves malicious intent | Unsupported | — |
| Riot discloses anti-cheat / background technologies in the Privacy Notice | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> | Officially documented |
| Exact telemetry schema for current Vanguard builds | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> | Unknown here |
| Signed driver ⇒ safe software | False as a general rule | See digital signatures concept |
| Investigation complete | No | Developing case study |

> **🔍 Finding**
>
> The credible security conclusion at this stage is about **trust and privilege**, not about proving secret malice or proving perfect safety: installing Riot Vanguard means trusting Riot (and the Windows signing / update ecosystem) with kernel-level code on the machine, in exchange for Riot’s competitive-integrity goals.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

| Source | Accessed |
| --- | --- |
| <a href="https://www.riotgames.com/en/privacy-notice" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Riot Games Privacy Notice</a> | 2026-10-10 |
| <a href="https://www.riotgames.com/en/news/a-message-about-vanguard-from-our-security-privacy-teams" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> A Message About Vanguard From Our Security & Privacy Teams</a> | 2026-10-10 |
| <a href="https://support-valorant.riotgames.com/hc/en-us/articles/360046160933-What-is-Riot-Vanguard" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> What is Vanguard?</a> | 2026-10-10 |
| <a href="https://www.riotgames.com/en/news/vanguard-on-demand" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Incoming: Vanguard On-Demand</a> | 2026-10-10 |
| <a href="https://www.riotgames.com/en/news/vanguard-security-update-motherboard" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Vanguard Security Update: Closing the Pre-Boot Gap</a> | 2026-10-10 |
| Microsoft Learn — Memory Integrity / VBS pages linked from the [technical overview](technical-overview.md) | 2026-10-10 |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Case overview](README.md)
- [Technical overview](technical-overview.md)
- [Services and drivers](services-and-drivers.md)
- [Investigation log](investigation-log.md)
