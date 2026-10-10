<h1 style="text-align:center">Threat assessment</h1>

[<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Concepts](README.md) · [<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../README.md)

A restrained framework for discussing risk without turning every unfamiliar artefact into a malware claim.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Short definition</h2>

**Threat assessment** in Fieldnotes means weighing observations, provenance, behaviour, and disclosures—with explicit confidence labels—before using strong security language.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Why it matters</h2>

Over-calling malware creates noise and harm. Under-documenting real abuse also fails readers. The goal is proportionate, evidence-bound language.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">How it works</h2>

Gather before judging:

1. **Observations** — registry values, files, processes, network endpoints actually seen
2. **Provenance** — distribution channel, signatures, hashes when collected
3. **Behaviour** — what runs, what persists, what contacts the network
4. **Disclosure** — privacy policy and vendor documentation
5. **Contradictions** — scanner results, community claims, and lab notes that disagree

Apply the shared [evidence legend](../README.md#evidence-legend).

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Practical example</h2>

| Behaviour | Evidence | Label | Security relevance |
| --- | --- | --- | --- |
| User `Run` values present | Registry query | <img src="../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> | Persistence-capable location; purpose pending |
| Kernel anti-cheat driver documented by vendor | Official Riot architecture posts | <img src="../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for “privileged component exists as product design” | Trust / TCB expansion — not a malware verdict by itself |

End with explicit non-claims when evidence is thin—as in the [Aniimo assessment](../applications/aniimo/investigation-log.md#interpretation-and-current-conclusions) or the [Vanguard assessment](../applications/valorant-vanguard/investigation-log.md#interpretation-and-current-conclusions).

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Common misconceptions</h2>

| Trap | Better practice |
| --- | --- |
| Treat telemetry SDKs as compromise | Treat as privacy lead until abuse is shown |
| Treat one clean AV quick scan as a full audit | Record it as a limited scan result |
| Treat unsigned code as malware by default | Verify path, behaviour, and provenance |
| Treat kernel-mode anti-cheat as malware by default | Document privilege and vendor claims; demand behavioural evidence for stronger labels |
| Escalate to “stealer” / “backdoor” without matching behaviours | Use precise, supported terms only |

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Security implications</h2>

- Do not publish secrets discovered during investigation.
- Green evidence labels mean confidence in a *claim*, not that software is safe.
- Prefer structured tables and open questions over dramatic conclusions.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Application investigation](../methodology/application-investigation.md)
- [Telemetry and analytics](telemetry-and-analytics.md)
- [Evidence and confidence](../methodology/evidence-and-confidence.md)
