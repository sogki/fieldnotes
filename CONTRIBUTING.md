<img src="assets/branding/fieldnotes-mark.svg" alt="Fieldnotes" width="40" height="40" style="display:block;margin:0 auto">
<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h1 style="text-align:center">Contributing</h1>

[<img src="assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](README.md)

Fieldnotes is a curated technical handbook. Contributions should improve accuracy, clarity, and navigability—not volume for its own sake.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Propose a new application profile</h2>

1. Create a directory under `applications/<app-name>/` using a short, lowercase slug.
2. Add a profile `README.md` that follows the [application investigation format](methodology/application-investigation.md).
3. Link the profile from [applications/README.md](applications/README.md).
4. Separate **observations**, **source-backed facts**, **interpretations**, and **hypotheses**.
5. Mark unknown fields as <img src="assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown**. Do not invent developer details, network behaviour, or security conclusions.

Suggested companions for larger investigations:

- `technical-overview.md` — install paths, processes, registry, networking
- `processes-and-startup.md` — process model, updater, and startup behaviour
- `privacy-and-telemetry.md` / `privacy-and-security.md` — analytics, SDKs, privacy claims, or privileged-software analysis
- `api-and-endpoints.md` — official vs public vs authenticated vs internal endpoints, with an evidence register
- `services-and-drivers.md` — when Windows services or kernel drivers are central
- `investigation-log.md` — chronology, commands, open questions

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Document commands consistently</h2>

Every command entry should include:

- One-sentence purpose
- Risk label (<img src="assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> / <img src="assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> / <img src="assets/branding/risk-destructive.svg" width="20" height="20" alt="Potentially destructive">)
- Requirements
- Syntax-highlighted command block
- What it does (parameters)
- Expected output (sanitised or clearly illustrative)
- How to interpret it
- Limitations
- Related commands
- Sources, where applicable

| Risk | Meaning |
| --- | --- |
| <img src="assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> **Read-only** | Observes state without changing it |
| <img src="assets/branding/risk-state.svg" width="20" height="20" alt="State-changing"> **State-changing** | Modifies configuration or system state |
| <img src="assets/branding/risk-destructive.svg" width="20" height="20" alt="Potentially destructive"> **Potentially destructive** | Can delete data, disrupt services, or cause irreversible change |

Prefer built-in operating-system tools. Explain when administrator privileges are required and why. Do not document remote download-and-execute patterns without explicit justification and risk labelling.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Cite sources</h2>

- Prefer official documentation, vendor privacy policies, reputable security research, and reproducible local observations.
- Include URLs and access dates when the source may change.
- Quote sparingly; paraphrase and attribute.
- Distinguish “vendor states X” from “we observed X”.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Report corrections</h2>

Open a pull request or issue that:

1. Identifies the file and claim.
2. States what is incorrect or incomplete.
3. Provides corrected text or evidence.
4. Updates confidence labels if the evidence changed.

Small factual fixes are preferred over broad rewrites.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Separate observations from assumptions</h2>

| Kind | Example |
| --- | --- |
| Observation | Value `AF_uuid_4126040` exists under the current user’s `Run` key |
| Source-backed fact | Steam lists Aniimo under application ID `4126040` |
| Interpretation | The numeric suffix may identify the associated Steam app |
| Hypothesis | The values may be written by an AppsFlyer-related component |

Never present a hypothesis as <img src="assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> **Confirmed**. A telemetry SDK or startup entry is not automatically malicious.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Visual identity</h2>

Hub pages (root README, section indexes) follow the pink/purple sakura-night look in [assets/branding](assets/branding/README.md): banner or mark, sakura rule, optional pink/purple shields. Investigation body text stays calm and technical — use only the shared evidence/risk SVG markers (white→red diamonds and shields); do not invent alternate colours or drown pages in decoration.

<h2 style="text-align:center">Markdown conventions</h2>

- One H1 per document; keep heading levels in order.
- Link back to the root [README.md](README.md) or the nearest section index.
- Prefer relative links and descriptive link text.
- Use tables for navigation and structured metadata—not every paragraph.
- Use meaningful emoji as section signposts; keep density low.
- Use blockquote callouts (`ℹ️ Note`, `⚠️ Caution`, `🔍 Finding`, `❓ Unverified`) sparingly.
- Apply the shared [evidence legend](README.md#evidence-legend).
- Prefer short paragraphs, fenced code blocks for commands, and inline code for identifiers.
- Avoid badge walls, motivational quotes, and decorative clutter beyond the shared branding accents.
- On new section indexes, prefer the mark + sakura-rule header pattern used in `applications/`, `commands/`, `concepts/`, and `methodology/`.
- Prefer the centered sakura rule (`sakura-rule.svg`) instead of Markdown `---` horizontal rules.
- Put forward arrows **beside** link text — do not use a dedicated icon column in tables.
- Prefer purple nav links: `<a href="…" style="color:#c77dff">` (GitHub may still force blue).
- Application case titles: category icon **inline** with a centered H1 (`icon-chat`, `icon-game`, or `icon-mouse`) — not stacked above the name.
- Center major headings (`h1`–`h3` and category labels) with `style="text-align:center"`.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Licence</h2>

Fieldnotes is released under the [MIT License](LICENSE). Contributions are accepted under the same terms unless stated otherwise.

<img src="assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Evidence and confidence](methodology/evidence-and-confidence.md)
- [Application investigation](methodology/application-investigation.md)
- [Security reporting](SECURITY.md)
