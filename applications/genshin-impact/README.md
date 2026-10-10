<h1 style="text-align:center"><img src="../../assets/branding/icon-game.svg" alt="" width="40" height="40" style="vertical-align:middle;margin-right:10px">Genshin Impact</h1>

<a href="../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Applications</a> · <a href="../../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes</a>

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">


**Genshin Impact** is an open-world action RPG published by HoYoverse (Cognosphere Pte. Ltd.). This profile combines **read-only local Windows observations** with research into official interfaces, the wish-history / Paimon.moe import ecosystem, and other community APIs.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Overview</h2>

| Field | Value | Confidence |
| --- | --- | --- |
| Application in scope | Genshin Impact (PC) + HoYoPlay | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official products |
| Publisher (install metadata) | COGNOSPHERE PTE. LTD. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local Uninstall registry |
| Installed game version (active config) | `7.1.0` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local `config.ini` |
| HoYoPlay version | `1.18.0.380` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local files + registry |
| Active game path (HYP registry) | `<DRIVE>:\Genshin Impact game` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local registry (path sanitised in companions) |
| Processes during snapshot | None (game/launcher not running) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local process query |
| Anti-cheat driver | `HoYoProtect` → `HoYoKProtect.sys` **Stopped / Manual** | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local CIM + file hash match to game copy |
| Official public game-data developer API | **None identified** in reviewed sources | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Docs search — not an absolute proof of non-existence |
| Malware determination | None made | Explicitly **not** claimed |

> **🕵️ Investigation status**
>
> Hands-on case study (2026-10-10 UTC) — local read-only inventory, plus a **line-cited** Paimon.moe / `paimon-moe-api` source review of wish import, `/corsproxy`, `addToVault`, `localforage`, and Google Drive sync. **Not** a completed security audit. Genshin Impact is **not** labelled malware, and it is **not** certified harmless.
>
> Wish-history URLs / `authkey` values were **not** extracted or published. No import was performed. Third-party repository code was **not** executed. The game was **not** launched for this investigation.

<h3 style="text-align:center">On this page</h3>

- [Purpose and scope](#purpose-and-scope)
- [Table of contents](#table-of-contents)
- [Brief assessment](#brief-assessment)
- [Safety boundaries](#safety-boundaries)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Purpose and scope</h2>

**Purpose:** Document how Genshin/HoYoPlay appear on this PC, what public/community APIs exist (especially wish-history import), and how evidence types differ.

**In scope**

- HoYoPlay + game install metadata, signatures, driver registration
- Process/service/startup snapshot (absent game processes)
- Wish-history `getGachaLog` flow as used by community tools
- Paimon.moe frontend + `paimon-moe-api` source behaviour
- Enka.Network and genshin.dev community APIs

**Out of scope**

- Launching the game or HoYoPlay for the investigation
- Reading `output_log.txt` / webCaches for authkeys
- Authenticated HoYoLAB cookie calls
- Anti-cheat bypass, memory inspection, packet interception

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

| Document | Role |
| --- | --- |
| <a href="technical-overview.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Technical overview</a> | Install layout, components, signatures |
| <a href="processes-and-startup.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Processes and startup</a> | Snapshot processes, `HoYoProtect`, startup |
| <a href="privacy-and-telemetry.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Privacy and telemetry</a> | Policy + local telemetry-related binaries |
| <a href="api-and-endpoints.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> API and endpoints</a> | Wish import data flow, vault/Drive details, endpoint register |
| <a href="investigation-log.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log</a> | Commands, chronology, verification record |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Brief assessment</h2>

| Claim | Label |
| --- | --- |
| Genshin is installed via HoYoPlay; active game dir is registered under HYP as `hk4e_global` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Game `config.ini` reports `game_version=7.1.0` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Key HoYoPlay / game binaries are Authenticode-signed by Cognosphere (driver via Microsoft Hardware Compatibility Publisher) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local signature checks |
| `HoYoProtect` was Stopped + Manual while the game was not running | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local — **does not prove** on-demand-only design forever |
| Signature validity proves absence of vulnerabilities or privacy concerns | **False / unsupported** |
| Wish history is retrieved via HoYoverse `getGachaLog` using a short-lived `authkey` from the in-game history webview | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Paimon importer constructs/calls it — **not** an official third-party developer API |
| Paimon.moe is a community project (MadeBaruna), not HoYoverse | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> GitHub ownership + homepage |
| Browser posts the full wish URL (including `authkey`) to Paimon `/corsproxy`, which fetches HoYoverse | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `import.svelte` + `corsProxy.ts` |
| `/corsproxy` enqueues `data.data.list` to Redis Bull queue `wish-vault` when host is hoyoverse/mihoyo | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> enqueue in source; **no consumer/TTL found in reviewed repo** (<img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> production fate — not proof of permanence) |
| Imported wish counters persist in browser `localforage`; optional Drive `appDataFolder`/`save.json`; optional tally POSTs (include `uid`) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `saveManager.js`, `DataSync.svelte`, `wishTally.js` |
| Official Discord-style public game-data API exists | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **None identified** in reviewed sources |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Safety boundaries</h2>

> **⚠️ Caution**
>
> Do **not** extract or paste wish-history URLs containing `authkey`. Do **not** run community `getlink.ps1` scripts as part of Fieldnotes evidence collection. Do **not** unload or tamper with `HoYoProtect`. Do **not** attach debuggers or inspect game memory.
>
> Read-only CIM/file queries are lower risk than state changes but are **not** guaranteed invisible to every monitoring system.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Local Windows observations recorded in the [investigation log](investigation-log.md) (2026-10-10)
- [HoYoverse Privacy Policy (2 April 2026)](https://fastcdn.hoyoverse.com/content-v2/hyg/159655/154d7f21f2183fdfe6092414f9afd1d6_1574794877928793488.pdf)
- [HoYoPlay](https://hoyoplay.hoyoverse.com/) / [HoYoPlay help](https://support.hoyoverse.com/hc/en-us/articles/52362516439577-What-should-I-do-if-I-encounter-issues-with-the-HoYoPlay-launcher)
- [MadeBaruna/paimon-moe](https://github.com/MadeBaruna/paimon-moe) · [MadeBaruna/paimon-moe-api](https://github.com/MadeBaruna/paimon-moe-api) · [paimon.moe](https://paimon.moe/)
- [EnkaNetwork/API-docs](https://github.com/EnkaNetwork/API-docs)
- [genshindev/api](https://github.com/genshindev/api) · hosted at [genshin.jmp.blue](https://genshin.jmp.blue/)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Processes and startup](processes-and-startup.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [API and endpoints](api-and-endpoints.md)
- [Investigation log](investigation-log.md)
