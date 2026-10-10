<h1 style="text-align:center">Telemetry and analytics</h1>

[<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Concepts](README.md) · [<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../README.md)

How Fieldnotes treats telemetry, analytics, and attribution SDKs—so registry values, library names, or network calls are not automatically labelled malicious.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Short definition</h2>

**Telemetry / analytics** here means software components that measure installs, sessions, crashes, feature use, or advertising attribution. Vendors may disclose this in privacy policies.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Why it matters</h2>

Presence of such a component is a **privacy and transparency** topic first. It becomes a **security** topic only with additional evidence—abuse, stealthy exfiltration of secrets, unwanted remote control, and similar.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">How it works (high level)</h2>

| Kind of data | Typical role |
| --- | --- |
| Analytics / attribution identifiers | Tie installs or sessions to campaigns or apps |
| Usage telemetry | Feature and performance metrics |
| Diagnostic logs | Crash and error reporting |
| Network-observed data | What actually leaves the device (only if captured) |

Mobile and desktop attribution platforms commonly rely on technical identifiers. AppsFlyer’s public documentation, for example, describes configurable collection of advertising and device identifiers for attribution and analytics, and its services privacy policy describes categories such as technical information, technical identifiers, and engagement information.

> **ℹ️ Note**
>
> Those materials describe a vendor’s general product behaviour. They do **not**, by themselves, prove that a specific local registry value was created by that vendor.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Practical example (illustrative pattern)</h2>

A Windows `Run` value named like `AF_uuid_<appid>` storing a GUID-style string is a **lead**, not proof of an analytics SDK. Establishing that requires writer/binary or traffic evidence for the installation under study.

Real case pointers (not completed audits):

- [Aniimo privacy and telemetry](../applications/aniimo/privacy-and-telemetry.md) — registry naming / analytics lead
- [VALORANT & Vanguard — privacy and security](../applications/valorant-vanguard/privacy-and-security.md) — vendor anti-cheat disclosures vs independent verification limits

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Common misconceptions</h2>

| Observation | Valid conclusion? |
| --- | --- |
| Value names resemble an analytics vendor’s naming | Lead for research — not proof of that vendor |
| Outbound HTTPS to a known analytics host | Evidence of network contact — not proof of malice |
| A GUID stored in the registry | Could be an install ID or unrelated data — purpose needs a reader/writer |
| Community post attributes a pattern to SDK X | <img src="../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> until verified on the binary or traffic under study |

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Security implications</h2>

- Separate “collects analytics” from “is malware”.
- Prefer vendor documentation and reproducible local evidence over forum consensus.
- Mark collected data types as <img src="../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** until supported by policy text or traffic analysis (with redaction).
- Never publish real device identifiers or raw GUIDs when a redacted form preserves the technical point.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📚 Further reading</h2>

| Source | Relevance | Access note |
| --- | --- | --- |
| <a href="https://support.appsflyer.com/hc/en-us/articles/4408847686161-Device-identifiers" style="color:#c77dff"><img src="../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> AppsFlyer — About device identifiers</a> | Identifier types used for attribution/analytics | Confirm wording when citing; accessed 2026-10-10 |
| <a href="https://www.appsflyer.com/legal/services-privacy-policy/" style="color:#c77dff"><img src="../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> AppsFlyer Services Privacy Policy</a> | Categories of end-user technical data | Confirm wording when citing; accessed 2026-10-10 |
| <a href="https://support.appsflyer.com/hc/en-us/articles/207032066-Basic-SDK-integration-guide" style="color:#c77dff"><img src="../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> AppsFlyer — Basic SDK integration guide</a> | AppsFlyer ID per install in their SDK model | General behaviour — not proof for a specific `Run` value |
| Microsoft Learn — Run and RunOnce registry keys | Legitimate autostart mechanism | See [startup items](../commands/windows/startup-items.md) |

Community forum posts may mention `AF_uuid_*` naming; treat them as leads, not confirmation of a local installation’s components.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Threat assessment](threat-assessment.md)
- [Aniimo investigation log](../applications/aniimo/investigation-log.md)
- [Networking commands](../commands/windows/networking.md)
