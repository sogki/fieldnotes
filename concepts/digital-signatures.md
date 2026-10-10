<h1 style="text-align:center">Digital signatures</h1>

[<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Concepts](README.md) · [<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../README.md)

What code signatures show—and do not show—during application investigation on Windows.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Short definition</h2>

A **digital signature** on a binary (for example Authenticode on Windows) cryptographically ties file contents to a publisher identity asserted by a certificate, when the signature chain is trusted at verification time.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Why it matters</h2>

Signatures help answer “who published this file?” and “was it altered after signing?” They do **not** answer “is this software desirable, private, or free of bugs?”

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">How it works</h2>

When verification succeeds:

- A publisher identity is asserted by the certificate subject (within CA practice limits).
- The signed content matches what was signed (integrity of that signed blob).

These points can support <img src="../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> or <img src="../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> statements about *publisher metadata as presented by the signature*, not about intent.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Practical example</h2>

When documenting files, record:

1. Full path and hash (when collected)
2. Signature status and publisher string
3. Verification time and whether the certificate was valid, expired, or untrusted

> **ℹ️ Note**
>
> Do not claim inspection that was not performed. Unsigned software is not automatically malicious.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Common misconceptions</h2>

| Belief | Reality |
| --- | --- |
| Valid signature means safe software | False — bugs, telemetry, and unwanted behaviour can still exist |
| Unsigned means malware | False — many legitimate tools are unsigned |
| Similar process name means same signed binary | False — always verify path |

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Security implications</h2>

- Prefer path + signature over process name alone.
- Signed code can still ship analytics SDKs or risky features.
- Accompanying unsigned scripts or later updates need separate trust decisions.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Processes](../commands/windows/processes.md)
- [Threat assessment](threat-assessment.md)
- [Application investigation](../methodology/application-investigation.md)
- Example signature-check template (driver path; run only voluntarily): [VALORANT & Vanguard — services and drivers](../applications/valorant-vanguard/services-and-drivers.md#d-inspect-a-driver-files-digital-signature)
