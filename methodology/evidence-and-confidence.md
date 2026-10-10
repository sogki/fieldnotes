<h1 style="text-align:center">Evidence and confidence</h1>

[<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Methodology](README.md) · [<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../README.md)

Fieldnotes uses a shared visual legend for confidence in individual findings—not as a safety rating for software.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Evidence legend</h2>

Markers are custom SVGs (not emoji). **Diamonds** = evidence confidence; **shields** = command risk. Both use a white→red heat scale: cooler / whiter = lower concern; redder = higher concern.

| Label | Meaning | Typical basis |
| --- | --- | --- |
| <img src="../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> **Confirmed** | Directly supported by reliable evidence | First-hand reproducible observation or primary authoritative source for the exact claim |
| <img src="../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> **Strongly supported** | Well supported but not airtight | Multiple credible sources or repeated observations with minor residual doubt |
| <img src="../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** | Reasonable explanation, unverified | Fits known patterns; lacks direct confirmation |
| <img src="../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** | Insufficient evidence | Gaps are explicit |
| <img src="../assets/branding/evidence-disputed.svg" width="20" height="20" alt="Disputed"> **Disputed** | Credible evidence conflicts | Document both sides and what would resolve the conflict |

<h3 style="text-align:center">Command risk (separate scale)</h3>

| Label | Meaning |
| --- | --- |
| <img src="../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only** | Observes state without changing it |
| <img src="../assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> **State-changing** | Modifies configuration or system state |
| <img src="../assets/branding/risk-destructive.svg" width="20" height="20" alt="Potentially destructive"> **Potentially destructive** | Can delete data, disrupt services, or cause irreversible change |

> **ℹ️ Note**
>
> Never use a pale / “confirmed” marker to imply that an application is completely safe.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Statement types</h2>

Keep these distinct in prose and tables:

| Type | Description |
| --- | --- |
| Observation | What was seen on a system or in a primary record |
| Source-backed fact | Claim grounded in a cited document or vendor statement |
| Interpretation | Meaning assigned to observations |
| Hypothesis | Testable explanation not yet verified |

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Applying labels</h2>

- Label the **claim**, not the whole file.
- Downgrade when evidence is indirect (name similarity alone → at most <img src="../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible**).
- Antivirus or scanner output is evidence about *that scan*, not a global proof of safety or guilt.
- Update labels when new evidence arrives; note the change in an investigation log when one exists.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Example</h2>

| Claim | Label |
| --- | --- |
| Value `AF_uuid_4126040` existed under the user’s `Run` key | <img src="../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> (if observed) |
| Suffix `4126040` is Aniimo’s Steam app ID | <img src="../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> with catalogue / library verification |
| Values were written by an AppsFlyer SDK inside Aniimo | <img src="../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> until writer/binary evidence exists |
| Aniimo is malware | Unsupported — do not state |

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Callout patterns</h2>

Use sparingly for emphasis:

> **🔍 Finding** — observation with clear support  
> **❓ Unverified** — hypothesis or incomplete attribution  
> **⚠️ Caution** — state-changing or risky commands  
> **ℹ️ Note** — context that prevents misreading

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Research principles](../README.md#research-principles)
- [Threat assessment](../concepts/threat-assessment.md)
- [Contributing](../CONTRIBUTING.md)
