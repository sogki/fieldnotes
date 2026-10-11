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
| Processes during 2026-10-11 play | `HYP.exe`, four `HYPHelper.exe`, `GenshinImpact.exe`, six `ZFGameBrowser.exe` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Image paths via `QueryFullProcessImageName` |
| Anti-cheat driver during play | `HoYoProtect` → `HoYoKProtect.sys` **Running / Manual** | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `sc.exe query` while the game process existed. **Stopped / Manual** at 17:40Z after the game exited, with HoYoPlay still open |
| Official public game-data developer API | **None identified** in reviewed sources | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Docs search — not an absolute proof of non-existence |
| Malware determination | None made | Explicitly **not** claimed |

> **🕵️ Investigation status**
>
> Hands-on case study. **2026-10-10** was a read-only inventory plus a line-cited Paimon.moe review, with the game closed. **2026-10-11** covers a normal play session: process tree, `HoYoProtect` Running, connection metadata, and a pass over public data APIs. **Not** a completed security audit. Genshin Impact is **not** labelled malware, and it is **not** certified harmless.
>
> Play was already underway when the first process list was taken, so there is no before-launch sample from the same run. The game was closed at about 17:39Z; the exit sample is in [Processes and startup](processes-and-startup.md). Wish-history URLs and `authkey` values were not extracted. Payloads were not decrypted. Account UID, client IP, and an updater key seen locally were not copied into these notes.

<h3 style="text-align:center">On this page</h3>

- [Purpose and scope](#purpose-and-scope)
- [Table of contents](#table-of-contents)
- [Brief assessment](#brief-assessment)
- [Safety boundaries](#safety-boundaries)
- [What the 2026-10-11 session establishes](#what-the-2026-10-11-session-establishes)
- [Still uncertain](#still-uncertain)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Purpose and scope</h2>

**Purpose:** Document how Genshin/HoYoPlay appear on this PC, what public/community APIs exist (especially wish-history import), and how evidence types differ.

**In scope**

- HoYoPlay + game install metadata, signatures, driver registration
- Read-only observation of a normal play session: processes, driver state, connection metadata
- Local logs and config only to the extent hostnames and non-secret fields can be recorded
- Wish-history `getGachaLog` as used by community tools
- Public catalogues that can feed community tools (genshin.dev, Amber/yatta, Hakushin, Enka docs)

**Out of scope**

- Starting, stopping, or interfering with the game
- Copying `authkey` values, cookies, UID, or the updater key from local files
- Reading web cache, Local Storage, or `UidInfo.txt`
- Authenticated HoYoLAB calls, packet capture, memory inspection, driver changes

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

| Document | Role |
| --- | --- |
| <a href="technical-overview.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Technical overview</a> | Install layout, components, signatures |
| <a href="processes-and-startup.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Processes and startup</a> | Snapshot processes, `HoYoProtect`, startup |
| <a href="privacy-and-telemetry.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Privacy and telemetry</a> | Policy + local telemetry-related binaries |
| <a href="api-and-endpoints.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> API and endpoints</a> | Wish import data flow, vault/Drive details, endpoint register |
| <a href="wish-history.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Wish History and account screens</a> | What those screens show, and where the PC recorded them |
| <a href="developer-features.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Developer features</a> | Catalogue of interfaces for community tools |
| <a href="investigation-log.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log</a> | Commands, chronology, verification record |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Brief assessment</h2>

| Claim | Label |
| --- | --- |
| Genshin is installed via HoYoPlay; active game dir is registered under HYP as `hk4e_global` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Game `config.ini` reports `game_version=7.1.0` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Key HoYoPlay / game binaries are Authenticode-signed by Cognosphere (driver via Microsoft Hardware Compatibility Publisher) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local signature checks |
| On 2026-10-11 `HoYoProtect` was Running and Manual while `GenshinImpact.exe` was running, and Stopped and Manual at 17:40Z after the game exited while HoYoPlay was still open | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `sc.exe query`. The load was not watched. The unload call was not traced |
| On 2026-10-10 the same driver was Stopped and Manual while no game process existed | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Earlier snapshot. It does not prove the driver never loads except during play |
| During play, `GenshinImpact.exe` held a TCP session to `8.211.9.13:8999`, an address also listed in a local `security_server_default_iplist_os_euro.txt` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Address match. Payload and exact service purpose were not read |
| Most other established sockets were TCP 443 to addresses whose PTR is CloudFront, Amazon Global Accelerator, or Google | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> PTR and owner pid. Origin hostname was not captured |
| Microsoft Defender had no current detection and no operational 1116 event in a 365-day query | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Tool output. Not a cleanliness certificate |
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

<h2 style="text-align:center">What the 2026-10-11 observations establish</h2>

The active global client is HoYoPlay `1.18.0.380` launching Genshin `7.1.0` (`hk4e_global`) from the HYP `GameInstallPath`. Those binaries, the in-game browser, and the telemetry-named libraries that were checked are Authenticode-valid and signed by Cognosphere. `HoYoKProtect.sys` is signed by Microsoft's hardware-compatibility publisher, matches the copy in the game folder, and was loaded with demand start while the game was open. At 17:40Z, after the game had exited and while HoYoPlay was still open, the same driver was Stopped.

Connection metadata shows the game process keeping TCP `8.211.9.13:8999` (Alibaba ASN 45102), which is the first address in a local file named `security_server_default_iplist_os_euro.txt`. Other established sockets were mostly TCP 443 to CloudFront, with one early Google address on a launcher helper and one early Amazon Global Accelerator address on the game. The Windows DNS cache was empty, so those 443 flows were not tied to a hostname. Encrypted payloads were not read.

Defender reported no current detection for this software. That is an observation about Defender's current list, not a verdict that the client is safe or malicious.

Community HTTP catalogues at `genshin.jmp.blue`, `gi.yatta.moe`, and `static.nanoka.cc` returned public game data without credentials. HoYoverse account and wish-history interfaces remain restricted. No official public game-data developer API was identified.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Still uncertain</h2>

| Gap | Evidence that would close it |
| --- | --- |
| Which process loads `HoYoProtect` | A same-session sample immediately before launch. The after-exit state is recorded: Stopped at 17:40Z while HoYoPlay was still open |
| Origin hostname of each TCP 443 socket | DNS cache, SNI, or a proxy log. Not packet decryption |
| What the port `8999` session carries | A stated protocol or a vendor document. Payload inspection was not done |
| Whether `ZFGameBrowser.exe` made its own connections | Denser sampling, or command lines that show which helper owns the network stack. During Wish History one browser pid did own port 443; see [Wish History and account screens](wish-history.md) |
| Whether launcher "data upload" sent a specific payload | The upload request itself. The log line only says the feature was started |
| Contents of `output_log.txt` beyond hostnames, and of web caches | Intentionally not copied, because they can hold `authkey` and account material |
| Post-exit leftovers | Sampled at 17:39Z. Game, in-game browser, and port 8999 were gone. `HYP.exe` and four `HYPHelper.exe` remained, and the launcher opened new port-443 sockets. No updater or crash process was present |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Local Windows observations recorded in the [investigation log](investigation-log.md) (2026-10-10 and 2026-10-11)
- [Developer features catalogue](developer-features.md) · [developer-features.yaml](developer-features.yaml)
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
- [Developer features](developer-features.md)
- [Investigation log](investigation-log.md)
