<h1 style="text-align:center">Genshin Impact — privacy and telemetry</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Vendor disclosures plus local artefacts that suggest telemetry/security tooling. Filenames and signed libraries are **not** packet proofs.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Vendor disclosures](#vendor-disclosures)
- [Local telemetry-related artefacts](#local-telemetry-related-artefacts)
- [Anti-cheat / security surface](#anticheat--security-surface)
- [Wish-history and third-party tools](#wish-history-and-third-party-tools)
- [What was not verified](#what-was-not-verified)
- [Assessment table](#assessment-table)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Vendor disclosures</h2>

HoYoverse Privacy Policy (version date **2 April 2026**) covers Genshin Impact, HoYoPlay, HoYoLab, and related services. Categories include account data, game data, device/IP data, advertising IDs, and **security-related data** (installed apps, plug-in/runtime data, network status, external plugin-related data, etc.).

Contact for privacy requests (policy): `privacy@hoyoverse.com`.

| Claim | Label |
| --- | --- |
| Collection categories are publicly disclosed | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official policy |
| Exact endpoints and payloads from this PC | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** (no packet capture) |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Local telemetry-related artefacts</h2>

Observed inside HoYoPlay `1.18.0.380` (signed Cognosphere unless noted):

| Artefact | Why it is privacy-relevant | Confidence |
| --- | --- | --- |
| `telemetry.dll` | Explicit telemetry module name | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> File present + signed |
| `MiHoYoMTRSDK.dll` | Metrics/telemetry SDK naming | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Filename |
| `HoYoDeviceFpSDK.dll` | Device fingerprint SDK naming | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Filename |
| `crashreport.exe` | Crash reporting helper | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Filename |
| `config.ini` `apm_*` switches | Application performance monitoring style flags | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Config keys |
| Channel / `uapc` fields in configs | Distribution/attribution style identifiers | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Present; values redacted in notes |

These support that HoYoPlay ships telemetry-oriented components. They do **not**, alone, prove covert collection beyond the privacy policy.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Anti-cheat / security surface</h2>

| Observation | Label |
| --- | --- |
| `HoYoKProtect.sys` registered as `HoYoProtect`, Stopped/Manual when game idle | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Same SHA-256 in `system32` and active game folder | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local hashes |
| Privacy policy lists security/plug-in related data categories | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |
| Kernel driver expands trust surface | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> General OS principle |
| Driver behaviour while playing on this PC | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not observed (game not launched) |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Wish-history and third-party tools</h2>

Community wish importers (including Paimon.moe) use a **temporary `authkey`** embedded in a game-generated wish-history URL. That key can read gacha history until it expires (`authkey timeout` in importer source — `import.svelte`).

| Path | What source shows | Confidence |
| --- | --- | --- |
| Paste URL into Paimon | Full URL (with `authkey`) is `POST`ed to Paimon `/corsproxy` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Paimon → HoYoverse | Backend fetches that URL via an egress proxy | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Browser persistence | Pity/history objects in `localforage` (`wish-counter-*`, `wish-uid`, …) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Google Drive (optional) | OAuth scope `drive.appdata`; file `save.json` in `appDataFolder` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Wish tally (optional, default on) | Aggregates + plaintext **`uid`** to Paimon `/wish*` — not anonymous; not the authkey URL | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `addToVault` | Enqueues unmodified **`data.data.list`** to Redis `wish-vault`; URL not queued; **no consumer / job TTL found in reviewed source** | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> enqueue / <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> production fate |
| Browser console | Importer `console.log(url)` prints the full history URL locally | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Infra / egress / edge logs | May capture the URL; **not verified** in Fieldnotes | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> / <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |

| Risk | Note |
| --- | --- |
| Sharing a wish URL | Temporary read access to that account’s wish history |
| Using Paimon’s importer | Authkey URL is intentionally sent to Paimon’s API and onward to HoYoverse (by design of the tool) |
| Assuming “local only” | Incorrect: corsproxy always sees the URL; Drive/tally optional extras |
| Calling vault “permanent store” | Unsupported — enqueue confirmed; retention unknown |

Detail and line citations: [API and endpoints](api-and-endpoints.md).

Fieldnotes did **not** extract or test a live authkey.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">What was not verified</h2>

- Live network destinations for `telemetry.dll` / MTR SDK  
- Whether APM switches can be user-disabled  
- Contents of `output_log.txt` / webCaches  
- Effectiveness of any in-game privacy toggles  

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Assessment table</h2>

| Finding | Label |
| --- | --- |
| HoYoverse discloses broad device/game/security data use | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| HoYoPlay ships signed modules named for telemetry / device fingerprint / crash reporting | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Those modules prove malicious spyware | **Not claimed** |
| Wish `authkey` is sensitive temporary credential material | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Community + importer source |
| Using Paimon.moe keeps all wish data exclusively on-device | **Not supported** — URL hits `/corsproxy`; localforage local; Drive + tally optional; list rows may be queued (`wish-vault` fate <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown">) |
| Tally data is anonymous | **Not supported** — client sends `uid` in JSON |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- [HoYoverse Privacy Policy (2 April 2026)](https://fastcdn.hoyoverse.com/content-v2/hyg/159655/154d7f21f2183fdfe6092414f9afd1d6_1574794877928793488.pdf)
- Local file/signature observations — [investigation log](investigation-log.md)
- [corsProxy.ts](https://github.com/MadeBaruna/paimon-moe-api/blob/main/src/routes/corsProxy.ts) · [vault.ts](https://github.com/MadeBaruna/paimon-moe-api/blob/main/src/queue/vault.ts)
- [saveManager.js](https://github.com/MadeBaruna/paimon-moe/blob/main/src/stores/saveManager.js) · [DataSync.svelte](https://github.com/MadeBaruna/paimon-moe/blob/main/src/components/DataSync.svelte)
- [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [API and endpoints](api-and-endpoints.md)
- [Technical overview](technical-overview.md)
