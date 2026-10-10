<h1 style="text-align:center">Discord — privacy and telemetry</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Official privacy controls plus local artefacts that indicate crash/error reporting infrastructure.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Vendor disclosures and controls](#vendor-disclosures-and-controls)
- [Local privacy-relevant artefacts](#local-privacy-relevant-artefacts)
- [What was not inspected](#what-was-not-inspected)
- [Assessment](#assessment)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Vendor disclosures and controls</h2>

Discord’s Privacy Policy and support articles document device/usage data collection and in-app **Data & Privacy** style toggles (improve Discord, personalize, sponsored content). Users may request a Data Package.

| Claim | Label |
| --- | --- |
| Disclosures and toggles exist | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official support/policy |
| Toggle effectiveness on this install’s wire traffic | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not measured |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Local privacy-relevant artefacts</h2>

| Artefact | Observation | Confidence |
| --- | --- | --- |
| `%APPDATA%\discord\sentry\` | Directory with `queue\` and `scope_v3.json` (~40 KB) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Names/sizes |
| `%APPDATA%\discord\Crashpad\` | Present | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `%APPDATA%\discord\logs\` | Present | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Module `discord_krisp` | Noise-suppression related module name | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> |
| CSP reporting host mentioned in prior Get Gateway HTTP headers | Cloudflare/Sentry CSP report endpoint in response headers during API check | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Prior/live header observation |

Interpretation: Discord ships client-side error/crash reporting components. That aligns with normal desktop practice and Discord’s own documentation about improving the product — **not** labelled malicious here.

> **⚠️ Caution**
>
> Do not open `scope_v3.json`, Local Storage, or log bodies for Fieldnotes — they may contain account or message-adjacent context. Do not send synthetic events to Sentry.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">What was not inspected</h2>

- Contents of Sentry scope/queue files  
- Local Storage token material  
- Packet capture of analytics hosts  
- Whether privacy toggles change Sentry event volume  

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Assessment</h2>

| Finding | Label |
| --- | --- |
| Discord documents optional analytics-style controls | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Local Sentry + Crashpad folders exist for Stable | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Presence of Sentry proves unlawful surveillance | **Not claimed** |
| Session tokens were reviewed | **Not done** (intentionally) |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- [Privacy Policy](https://discord.com/privacy)
- [Data Privacy Controls](https://support.discord.com/hc/en-us/articles/360004109911-Data-Privacy-Controls)
- [Data Used to Improve Discord](https://support.discord.com/hc/en-us/articles/21864805694999-Data-Used-to-Improve-Discord)
- Local folder listing — [investigation log](investigation-log.md)
- [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [API and endpoints](api-and-endpoints.md)
- [Technical overview](technical-overview.md)
