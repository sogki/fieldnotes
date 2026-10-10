<img src="assets/branding/fieldnotes-mark.svg" alt="Fieldnotes" width="40" height="40" style="display:block;margin:0 auto">
<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h1 style="text-align:center">Security</h1>

[<img src="assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](README.md)

Fieldnotes is a documentation repository. It does not ship production software that processes untrusted input. Contributions and published notes can still create security or privacy harm if handled carelessly.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Responsible disclosure</h2>

If you discover a vulnerability in **software discussed in Fieldnotes**, report it through the vendor’s official security contact, bug bounty programme, or coordinated disclosure process. Do not use this repository as the primary channel for unpatched vendor vulnerabilities.

If you discover a problem in **this repository’s documentation** that could cause harm if followed—for example, a command incorrectly labelled as safe, or guidance that exposes credentials—open an issue or pull request that corrects the text without including secrets.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Do not publish sensitive material</h2>

Do not commit or paste:

- Passwords, API keys, tokens, or session cookies
- Private keys or certificate material
- Personal data, account identifiers, or contact details of third parties
- Full memory dumps, packet captures, or logs that contain secrets
- Exploit details intended for immediate abuse of unpatched systems

When documenting investigations, redact hostnames, account names, and unique identifiers that are not necessary to understand the finding. Prefer placeholders such as `[REDACTED GUID]` and `<STEAM_INSTALL_ROOT>`.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Scope of claims</h2>

Fieldnotes records observations and research notes. Presence here does not mean an application is malicious, vulnerable, or safe. Readers should verify behaviour on their own systems and consult vendor documentation.

> **ℹ️ Note**
>
> Evidence labels (<img src="assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Confirmed, <img src="assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> Plausible, and so on) describe confidence in a claim—not a safety rating for software.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Contact expectations</h2>

No dedicated security mailbox or response SLA is defined for this repository at this time. Prefer public issues or pull requests for documentation problems that can be discussed without exposing sensitive data. For vendor product issues, use the vendor’s published reporting channel.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Contributing](CONTRIBUTING.md)
- [Threat assessment](concepts/threat-assessment.md)
- [Root README](README.md)
