<h1 style="text-align:center">Application investigation</h1>

[<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Methodology](README.md) · [<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../README.md)

Produce a reproducible, evidence-labelled profile of an application’s behaviour without inventing gaps or declaring malware without support.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Goal</h2>

A structured investigative dossier: what was seen, what it supports, and what remains open.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Standard profile sections</h2>

Use this order in each application `README.md` (deepen in companion files as needed):

1. Overview
2. Developer and publisher
3. Official distribution channels
4. Installation and update behaviour
5. Files and directories
6. Processes and services
7. Registry entries and startup behaviour
8. Network activity
9. Permissions and persistence
10. Privacy and telemetry
11. Security considerations
12. Diagnostic commands
13. Findings and unresolved questions
14. Sources

Mark unavailable sections <img src="../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** rather than omitting silently when the section is in scope.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Recommended workflow</h2>

1. **Define scope** — platform, version, distribution channel, questions to answer.
2. **Record environment** — OS edition, how the app was installed, live use vs lab.
3. **Collect read-only baselines** — startup, registry, processes, services, network as appropriate.
4. **Attribute cautiously** — match files and PIDs before naming a vendor component.
5. **Compare disclosures** — privacy policy and official docs versus observations.
6. **Write with labels** — apply [evidence and confidence](evidence-and-confidence.md).
7. **List open questions** — and what evidence would close them.
8. **Link navigation** — section index, related concepts, and root README.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Companion files</h2>

For larger cases (see [Aniimo](../applications/aniimo/README.md) and [VALORANT & Vanguard](../applications/valorant-vanguard/README.md)):

| File | Use |
| --- | --- |
| `technical-overview.md` | Extended system artefacts |
| `processes-and-startup.md` | Process model, updater, and startup behaviour when that is a focus |
| `privacy-and-telemetry.md` / `privacy-and-security.md` | Analytics hypotheses, privacy evidence, or privileged-software security analysis |
| `api-and-endpoints.md` | Official, public, authenticated, and internal/undocumented network interfaces with an evidence register |
| `services-and-drivers.md` | When Windows services/drivers are central (for example anti-cheat) |
| `investigation-log.md` | Chronology, commands, next steps |

Examples with API-focused companions: [Discord](../applications/discord/README.md), [Genshin Impact](../applications/genshin-impact/README.md).

Put the full command history in the investigation log; keep the profile overview concise. For anti-cheat targets, document voluntary read-only templates rather than auto-executing diagnostics against protected software.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Integrity rules</h2>

- Do not claim to have run commands, captured traffic, or inspected binaries unless that work was done.
- Do not present community speculation as <img src="../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> **Confirmed**.
- Do not treat unfamiliar persistence or telemetry as proof of malicious activity.
- Prefer built-in diagnostic commands under [commands/](../commands/README.md).

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Evidence and confidence](evidence-and-confidence.md)
- [Threat assessment](../concepts/threat-assessment.md)
- [Contributing](../CONTRIBUTING.md)
