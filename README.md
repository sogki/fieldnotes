<img src="assets/branding/fieldnotes-banner.png" alt="Fieldnotes — investigate, document, understand, stay safe" width="100%" style="display:block;margin:0 auto">
<div align="center">

<em>Understanding the software behind the screen.</em>

<a href="#explore-fieldnotes"><img src="https://img.shields.io/badge/investigate-e05697?style=flat-square" alt="Investigate"></a>
<a href="#featured-investigations"><img src="https://img.shields.io/badge/document-9b4dca?style=flat-square" alt="Document"></a>
<a href="#research-principles"><img src="https://img.shields.io/badge/understand-ff7eb6?style=flat-square" alt="Understand"></a>
<a href="SECURITY.md"><img src="https://img.shields.io/badge/stay%20safe-e05697?style=flat-square" alt="Stay safe"></a>

</div>
<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

Fieldnotes is a curated, Markdown-only knowledge base for researching applications, operating-system behaviour, diagnostic commands, privacy practices, and security-relevant system artefacts.

It is a personal investigation and documentation project for the software, games, and services on this PC — a technical handbook, not a security product, vulnerability database, or completed audit of any application. Findings are evidence-labelled; unknowns stay explicit.

Visual identity: <a href="assets/branding/README.md" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> assets/branding</a>.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Contents</h2>

| Destination | What you’ll find |
| --- | --- |
| <a href="#explore-fieldnotes" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Explore Fieldnotes</a> | Section map — apps, commands, concepts, method |
| <a href="#investigations-by-category" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigations by category</a> | Games, applications, websites |
| <a href="#featured-investigations" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Featured investigations</a> | Case-study write-ups |
| <a href="#research-principles" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Research principles</a> | How Fieldnotes treats evidence and unknowns |
| <a href="#evidence-legend" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Evidence legend</a> | White→red confidence markers |
| <a href="#contributing" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Contributing</a> | Profile structure and Markdown conventions |
| <a href="#security-reporting" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Security reporting</a> | Secrets, disclosure, and what not to publish |
| <a href="#licence" style="color:#c77dff"><img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Licence</a> | MIT |

<h3 style="text-align:center">Investigations by category</h3>

<h4 style="text-align:center"><img src="assets/branding/icon-game.svg" alt="" width="30" height="30" style="vertical-align:middle;margin-right:14px">Games</h4>

| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Aniimo](applications/aniimo/README.md) | [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> VALORANT & Riot Vanguard](applications/valorant-vanguard/README.md) | [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Genshin Impact](applications/genshin-impact/README.md) |
| --- | --- | --- |

<h4 style="text-align:center"><img src="assets/branding/icon-chat.svg" alt="" width="30" height="30" style="vertical-align:middle;margin-right:14px">Applications</h4>

| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Discord](applications/discord/README.md) | | |
| --- | --- | --- |

<h4 style="text-align:center"><img src="assets/branding/icon-web.svg" alt="" width="30" height="30" style="vertical-align:middle;margin-right:14px">Websites</h4>

| *None yet* | | |
| --- | --- | --- |

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Explore Fieldnotes</h2>

| Section | What you will find |
| --- | --- |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Application investigations](applications/README.md) | Structured profiles of specific applications and observed behaviour |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Command reference](commands/README.md) | Diagnostic commands with purpose, output guidance, and risk labels |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Security and privacy concepts](concepts/README.md) | Telemetry, signatures, persistence, and threat assessment |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation methodology](methodology/README.md) | How investigations are planned, documented, and qualified |

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Featured investigations</h2>

<h3 style="text-align:center">Aniimo — Windows `Run` values and Steam app ID `4126040`</h3>

> **🕵️ Ongoing investigation**
>
> A 2026-10-11 session inspected Aniimo on Steam and then from the Epic Games launcher. Both builds contain FunPlus `FPX` binaries with AppsFlyer endpoints. The Steam-only kernel driver was stopped while the Epic process was running. No AppsFlyer connection was observed, and `759396` remains unresolved.
>
> This is **not** a completed security audit. Aniimo is **not** labelled malware, and it is **not** certified harmless.

| Document | Role |
| --- | --- |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Case overview](applications/aniimo/README.md) | Concise dossier and current assessment |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log](applications/aniimo/investigation-log.md) | Chronology, commands, sanitised results |

<h3 style="text-align:center">VALORANT & Riot Vanguard — privileged anti-cheat case study</h3>

> **🕵️ Developing investigation**
>
> Study of Riot Vanguard’s architecture and Pre-Check / on-demand model from official docs, plus a limited read-only local inventory: `vgc` and `vgk` were **Stopped** with **Manual** start mode. That pattern is observed locally; on-demand as the explanation remains unverified. Kernel privilege is a trust surface—not proof of malice.
>
> This is **not** a completed security audit. Vanguard is **not** labelled malware, and it is **not** certified harmless.

| Document | Role |
| --- | --- |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Case overview](applications/valorant-vanguard/README.md) | Scope, status, and assessment |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Technical overview](applications/valorant-vanguard/technical-overview.md) | Architecture, Pre-Check / on-demand, Windows requirements |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Services and drivers](applications/valorant-vanguard/services-and-drivers.md) | Local `vgc` / `vgk` observations and safe queries |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log](applications/valorant-vanguard/investigation-log.md) | Research chronology and sanitised CIM results |

<h3 style="text-align:center">Discord — desktop architecture and official APIs</h3>

> **🕵️ Hands-on investigation**
>
> Local Stable `1.0.9261` inventory: Squirrel install under `%LOCALAPPDATA%\Discord`, six `Discord.exe` processes, Run-key launch via `Update.exe`, Discord-signed binaries, and `sentry`/`Crashpad` roaming artefacts. Official REST / Gateway / OAuth2 documented; `GET /api/v10/gateway` re-checked. Tokens and Local Storage were not read.
>
> This is **not** a completed security audit. Discord is **not** labelled malware, and it is **not** certified harmless.

| Document | Role |
| --- | --- |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Case overview](applications/discord/README.md) | Scope, status, and assessment |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Processes and startup](applications/discord/processes-and-startup.md) | Process tree and updater launch chain |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> API and endpoints](applications/discord/api-and-endpoints.md) | Official, public, authenticated, and internal categories |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log](applications/discord/investigation-log.md) | Commands and live verification |

<h3 style="text-align:center">Genshin Impact — launcher, privacy, wish history, and APIs</h3>

> **🕵️ Hands-on investigation**
>
> Local HoYoPlay `1.18.0.380` + Genshin `7.1.0` (`hk4e_global`): Cognosphere-signed binaries, idle `HoYoProtect` driver, no game processes at snapshot. Wish-history `getGachaLog` + Paimon.moe import/proxy flow documented from source; Enka and genshin.dev checked as third-party. Authkeys and game launch were avoided.
>
> This is **not** a completed security audit. Genshin Impact is **not** labelled malware, and it is **not** certified harmless.

| Document | Role |
| --- | --- |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Case overview](applications/genshin-impact/README.md) | Scope, status, and assessment |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> API and endpoints](applications/genshin-impact/api-and-endpoints.md) | Wish history, Paimon.moe, Enka, genshin.dev |
| [<img src="assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log](applications/genshin-impact/investigation-log.md) | Commands and verification |

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Research principles</h2>

- **Evidence before conclusions.** Record what was observed, where, and how it can be reproduced.
- **Observations separated from hypotheses.** Label interpretations and community leads clearly.
- **Sources cited wherever practical.** Prefer official docs, vendor policies, and reproducible checks.
- **Unknowns documented honestly.** Do not fill gaps with speculation presented as fact.
- **Destructive commands clearly identified.** Risk labels sit next to the commands they describe.

A registry entry, unfamiliar process, analytics SDK, or network connection is not proof of malicious activity.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Evidence legend</h2>

These labels describe confidence in individual findings—not whether software is inherently safe or malicious. Markers are **diamonds** on a white→red heat scale (cooler / whiter = firmer evidence; redder = more uncertain or conflicted).

| Label | Meaning |
| --- | --- |
| <img src="assets/branding/evidence-confirmed.svg" width="22" height="22" alt=""> **Confirmed** | Directly supported by reliable evidence |
| <img src="assets/branding/evidence-strong.svg" width="22" height="22" alt=""> **Strongly supported** | Supported by multiple credible sources or reproducible observations |
| <img src="assets/branding/evidence-plausible.svg" width="22" height="22" alt=""> **Plausible** | A reasonable but unverified explanation |
| <img src="assets/branding/evidence-unknown.svg" width="22" height="22" alt=""> **Unknown** | Insufficient evidence |
| <img src="assets/branding/evidence-disputed.svg" width="22" height="22" alt=""> **Disputed** | Credible evidence conflicts |

Command risk uses a separate **shield** scale (same heat colours):

| Label | Meaning |
| --- | --- |
| <img src="assets/branding/risk-readonly.svg" width="22" height="22" alt=""> **Read-only** | Observes state without changing it |
| <img src="assets/branding/risk-state.svg" width="22" height="22" alt=""> **State-changing** | Modifies configuration or system state |
| <img src="assets/branding/risk-destructive.svg" width="22" height="22" alt=""> **Potentially destructive** | Can delete data, disrupt services, or cause irreversible change |

Full guidance: [Evidence and confidence](methodology/evidence-and-confidence.md).

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Contributing</h2>

See [CONTRIBUTING.md](CONTRIBUTING.md) for profile structure, command documentation rules, citation expectations, Markdown conventions, and [visual identity](assets/branding/README.md).

<h2 style="text-align:center">Security reporting</h2>

Do not publish secrets or personal data here. Guidance: [SECURITY.md](SECURITY.md).

<h2 style="text-align:center">Licence</h2>

Released under the [MIT License](LICENSE). Copyright (c) 2026 sogki.
