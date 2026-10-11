<h1 style="text-align:center">🕵️ Genshin Impact — investigation log</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Hands-on research log (2026-10-10 UTC). Paths and usernames sanitised. Wish URLs / authkeys / UIDs not published.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Summary</h2>

| Field | Value |
| --- | --- |
| **Status** | Hands-on case study — 2026-10-10 inventory and Paimon review; 2026-10-11 play-session observation and public API checks |
| **OS** | Windows 11 Pro 64-bit 10.0.26300; PowerShell 5.1 |
| **Install** | HoYoPlay 1.18.0.380 + Genshin `hk4e_global` game_version 7.1.0 |
| **Processes** | 2026-10-10: none. 2026-10-11: `HYP.exe`, four `HYPHelper.exe`, `GenshinImpact.exe`, six `ZFGameBrowser.exe` during play. At 17:39Z the game and browser were gone; the launcher and helpers remained |
| **Driver** | 2026-10-10 Stopped/Manual. 2026-10-11 Running/Manual while the game process existed. Stopped/Manual at 17:40Z after exit, launcher still open. Copies share one SHA-256 |
| **API focus** | Wish `getGachaLog` (source only); genshin.dev; gi.yatta.moe; static.nanoka.cc; Enka error path |

<h3 style="text-align:center">Explicitly not performed</h3>

- HoYoPlay and the game were already running at the 16:42Z process list. They were not started for these notes. The game later exited at 17:39Z; that sample is recorded separately
- Copying `authkey`, UID, client IP, updater `AppKey`, or web-cache contents into the repo
- Opening `UidInfo.txt` or browser Local Storage
- Running `getlink.ps1` or importing wishes
- Authenticated HoYoLAB or `getGachaLog` live calls
- Installing or executing Paimon.moe / `paimon-moe-api` code
- OAuth / Google account access
- Debugger, memory inspection, packet capture, driver changes
- A sample from immediately before launch. The after-exit sample is 17:39Z–17:40Z  

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Chronology](#chronology)
- [Commands executed](#commands-executed)
- [Live endpoint checks](#live-endpoint-checks)
- [10. Play session and public catalogues (2026-10-11)](#10-play-session-and-public-catalogues-2026-10-11)
- [Interpretation](#interpretation)
- [Follow-up](#follow-up)
- [Non-claims](#non-claims)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Chronology</h2>

<h3 style="text-align:center">1. Environment and ARP detection</h3>

**Observation:** Uninstall entries for Genshin Impact 7.1.0 and HoYoPlay 1.18.0.380 (Publisher COGNOSPHERE PTE. LTD.); HoYoPlay path `C:\Program Files\HoYoPlay`.

**Confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">

<h3 style="text-align:center">2. HoYoPlay filesystem + signatures</h3>

**Observation:** Versioned tree with CEF/Qt, `telemetry.dll`, `MiHoYoMTRSDK.dll`, `HoYoDeviceFpSDK.dll`, updater/crash tools. Sample binaries Authenticode Valid (Cognosphere).

**Confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">

<h3 style="text-align:center">3. Game path resolution</h3>

**Observation:** `HKCU\Software\Cognosphere\HYP\1_0\hk4e_global\GameInstallPath` → active game dir; `config.ini` `game_version=7.1.0`. Additional older/alternate trees exist.

**Confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for active path; <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> that the `<DRIVE>:` tree is the one players launch (registry says so)

<h3 style="text-align:center">4. Driver / startup / processes</h3>

**Observation:** No game processes; no HoYo Run keys; `HoYoProtect` Stopped/Manual; `HoYoKProtect.sys` in system32 matches game folder SHA-256.

**Confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">

<h3 style="text-align:center">5. LocalLow artefacts (names only)</h3>

**Observation:** `output_log.txt` present; contents not read.

**Confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> existence

<h3 style="text-align:center">6. Wish / Paimon source study (initial)</h3>

**Observation:** Importer rewrites to `public-operation-hk4e-sg.hoyoverse.com/gacha_info/api/getGachaLog`; browser posts to Paimon `/corsproxy`; client uses `localforage` + optional Drive.

**Confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for source behaviour

<h3 style="text-align:center">7. Community API live checks</h3>

**Observation:** `genshin.jmp.blue` `/` and `/characters` returned 200 JSON. Enka example previously verified.

**Confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for those requests

<h3 style="text-align:center">8. Deep Paimon data-flow review (source text only)</h3>

**Repos verified:** [MadeBaruna/paimon-moe](https://github.com/MadeBaruna/paimon-moe) and [MadeBaruna/paimon-moe-api](https://github.com/MadeBaruna/paimon-moe-api), default branch `main` (2026-10-10). Source read as text; no `npm install`, builds, or service starts.

**Observation (corrections vs earlier phrasing):**

| Topic | Earlier phrasing | Current source evidence |
| --- | --- | --- |
| Request path | Proxy through Paimon | Confirmed: browser `POST {url}` → `/corsproxy` → egress proxy → HoYoverse `getGachaLog` |
| `addToVault` | “may store” / “vault” | Enqueues **`data.data.list` only** to Bull queue `wish-vault` when host ends with hoyoverse/mihoyo; **URL not queued** |
| Vault persistence | Implied permanent store risk | **No `queue.process` for `wish-vault` in repo**; production consumer/retention <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |
| Drive | Optional sync | Confirmed: OAuth `drive.appdata`, file `save.json` in `appDataFolder` |
| Tally | Mentioned lightly | Optional POSTs to `/wish*` (checkbox default **true**) of aggregates + `uid` |

**Primary files:** `paimon-moe/src/routes/wish/import.svelte`, `src/stores/saveManager.js`, `src/components/DataSync.svelte`, `src/functions/wishTally.js`; `paimon-moe-api/src/routes/corsProxy.ts`, `src/queue/vault.ts`, `src/stores/proxy.ts`, `src/index.ts`.

**Confidence:** <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> source behaviour; <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> production Redis worker / retention / logging of authkey URLs

**Docs updated:** [api-and-endpoints.md](api-and-endpoints.md), [privacy-and-telemetry.md](privacy-and-telemetry.md), [README.md](README.md).

<h3 style="text-align:center">9. Final pre-publication privacy / evidence review</h3>

**Source re-read (text only, 2026-10-10):** `corsProxy.ts` L35–75, `vault.ts` (full), `index.ts` L20–42, `proxy.ts` L29–45, `wish.ts` queue (contrast consumer), `wishTally.js`, `import.svelte` L119/L247/L269, `docker-compose.yml` env list, API README.

**Corrections / tightenings:**

| Item | Action |
| --- | --- |
| Vault retention | Clarified: **no consumer found** and **no job TTL found** in reviewed source — **not** “proven absent everywhere”; **not** “permanently retained” |
| Authkey logging | Documented browser `console.log(url)`; Fastify error-level logger; infra/egress logging unknown |
| Tally anonymity | Explicitly rejected — plaintext `uid` in POSTs |
| Official public API | Wording → “none identified in reviewed sources” |
| Privacy scrub | Enka example UID redacted from log; drive letters in technical overview generalised |

**Privacy audit of Fieldnotes genshin files:** No real authkeys, cookies, or wish URLs found. No Windows usernames. Standard `C:\Program Files\HoYoPlay` path retained. Drive letters generalised as `<DRIVE>` / `<OTHER_DRIVE>` (including residual prose references scrubbed in the final pre-publication review).

<h3 style="text-align:center">10. Play session and public catalogues (2026-10-11)</h3>

**Question:** What is actually running, loaded, and connected while the installed client is in normal use, and which public HTTP interfaces return game data without account credentials?

**Method:** Read-only CIM, `sc.exe`, `QueryFullProcessImageName` (`PROCESS_QUERY_LIMITED_INFORMATION`), `Get-NetTCPConnection`, PTR lookups, Authenticode and hash checks, Defender cmdlets, registry and startup filters, shared-read hostname extraction from logs, and single GETs of public URLs. HoYoPlay and the game were already running; they were not started for these notes. Creation times begin at 16:42:08Z. The first live query was about 16:45Z. The game was still running at 16:54:57Z and had exited by about 17:39Z. The exit sample is in [Processes and startup](processes-and-startup.md).

**Observed:**

| Topic | Result |
| --- | --- |
| Images | `HYP.exe` and four `HYPHelper.exe` from `C:\Program Files\HoYoPlay\1.18.0.380`. `GenshinImpact.exe` from the active `GameInstallPath`. Six `ZFGameBrowser.exe` from `GenshinImpact_Data\Plugins`, started 16:44:43Z |
| WMI | `ExecutablePath` and `CommandLine` blank. Limited image-name query succeeded |
| Driver | `HoYoProtect` kernel, demand start, **Running**, stoppable, not pausable. SHA-256 of `system32` and game copies match: `3C5129295AD93E9AD4FABC54940955915E79375C43A778E097DEC2DE486684DD` |
| Startup | No matching Run values, Startup-folder items, or scheduled tasks. No matching user-mode service |
| TCP | Game kept `8.211.9.13:8999` across samples. That address is first in `security_server_default_iplist_os_euro.txt` (also `47.91.86.250`, `8.211.3.101`). RIPE: AS45102 Alibaba. Other established remotes were mostly CloudFront:443, plus one Google helper socket and one Global Accelerator socket in the first sample only. Loopback pair on the game process |
| DNS cache | `Get-DnsClientCache` count 0. Origin names for 443 were not recovered |
| Browser processes | No TCP rows for the six `ZFGameBrowser.exe` pids after they existed |
| Defender | Real-time protection on. `Get-MpThreatDetection` count 0. No exclusion match. No Defender 1116 events in a 365-day filter |
| Local info file | `info.txt` at launch contains version, hardware fields, a UID, and a client IP. UID and IP not copied. `Crashes` had zero files. `UidInfo.txt` not opened |
| Launcher log | 16:42Z block logs data-upload/kibana start, updater AppId `604565`, and an AppKey that was not copied |
| Inactive tree | Standalone path on another drive, `game_version=5.1.0`, exe last write 2024-10-09. Not the running image |
| Public HTTP | genshin.dev character, yatta avatar list (136), nanoka manifest and character list (152 for `7.1.51`), webstatic version file `{"version":16}`, Enka UID `0` → 400, dispatch URL → 200 with a 16-character non-JSON body. `api.ambr.top` and `api.hakush.in` did not resolve |

**Supports:** The running images are the signed HoYoPlay and active-game files. The demand-start driver was loaded during play. One game socket matches a local security-server list on Alibaba address space. Community static APIs returned game-reference JSON without credentials.

**Does not establish:** Which process loaded the driver, the unload call itself, the hostname behind each CloudFront socket, the contents of the `8999` session, that telemetry DLLs contacted the host strings embedded in them, or that Defender has attested every file. The after-exit state is recorded separately: `HoYoProtect` was Stopped at 17:40Z.

**Errors and limits:** `output_log.txt` could not be opened with a normal read because the game had it locked; a shared read was used for URL hosts only. `Win32_Process` did not return command lines. Parent pid `22992` had already exited; attribution to the launcher bootstrap is from `launcher.log` lines that use that pid. Firewall application rules were not enumerated. `HKCR` was not fully searched (`HKCU\Software\Classes` had no matching key names).

**Docs updated:** [README](README.md), [technical overview](technical-overview.md), [processes and startup](processes-and-startup.md), [privacy and telemetry](privacy-and-telemetry.md), [API and endpoints](api-and-endpoints.md), [developer features](developer-features.md), [developer-features.yaml](developer-features.yaml).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h3 style="text-align:center">11. Wish History baseline (2026-10-11 17:09:36Z)</h3>

**Question:** What is already running and connected before Wish History is opened?

**Method:** `Get-Process` for the HoYoPlay and game names, `Win32_SystemDriver` for `HoYoProtect`, `Get-NetTCPConnection` limited to those pids and `Established` remotes other than `127.0.0.1`, and the non-secret keys in the active `config.ini`.

**Observed:** Same process tree as the 16:42Z launch, including six `ZFGameBrowser.exe` processes that started at 16:44Z. Driver still Running / Manual. Three remote TCP sockets, all on `GenshinImpact.exe`: `8.211.9.13:8999`, `18.165.160.33:443`, `3.173.182.118:443`. Client still `7.1.0` / `hk4e_global`.

**Supports:** A diff after the Wish screen is opened can ignore these processes and sockets unless they change.

**Does not establish:** Wish History contents, or that any current socket is the history API. `getGachaLog` remains source-only.

Detail: [wish-history.md](wish-history.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Commands executed</h2>

All <img src="../../assets/branding/risk-readonly.svg" width="20" height="20" alt="Read-only"> read-only unless noted. Patterns (not full raw dumps):

```powershell
# ARP narrow filter
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*,
  HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*,
  HKCU:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
  Where-Object { $_.DisplayName -match 'Genshin|HoYoPlay|HoYoverse|miHoYo|Discord' }

Get-CimInstance Win32_Process | Where-Object { $_.Name -match 'Genshin|HoYo|miHoYo|HYP|Discord' }

Get-ChildItem 'C:\Program Files\HoYoPlay' -Recurse -File -Include *.exe,*.dll,*.sys

Get-AuthenticodeSignature <identified binaries>
Get-FileHash 'C:\WINDOWS\system32\HoYoKProtect.sys', '<GAME>\HoYoKProtect.sys' -Algorithm SHA256

Get-CimInstance Win32_SystemDriver | Where-Object { $_.Name -match 'mhy|hoyo|genshin|hk4e' }
Get-CimInstance Win32_StartupCommand | Where-Object { $_.Command -match 'Discord|HoYo|Genshin' }

# Registry path keys under HKCU:\Software\Cognosphere\HYP (values filtered/redacted)
```

Failures: one PowerShell parse error on a signature pipeline (retried successfully); `UnityPlayer.dll` signature probe failed on active install because the file was absent at that path.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Live endpoint checks</h2>

| When | Request | Auth | Result | Limits |
| --- | --- | --- | --- | --- |
| 2026-10-10 | `GET https://genshin.jmp.blue/` | None | 200; types JSON | Static API only |
| 2026-10-10 | `GET https://genshin.jmp.blue/characters` | None | 200; id array | Truncated in notes |
| Prior session | `GET https://enka.network/api/uid/<docs-example-uid>/?info` | None | 200 | Path uses Enka docs example; UID not retained in Fieldnotes |
| — | `getGachaLog` / Paimon `/corsproxy` | — | **Not tested** | Would need authkey |
| 2026-10-11 | `GET https://genshin.jmp.blue/` and `/characters/ayaka?lang=en` | None | 200 JSON. Types listed; Ayaka record | Static community data |
| 2026-10-11 | `GET https://gi.yatta.moe/api/v2/en/avatar` | None | 200 JSON, 136 items | `api.ambr.top` did not resolve |
| 2026-10-11 | `GET https://static.nanoka.cc/manifest.json` and `/gi/7.1.51/character.json` | None | 200 JSON. `gi.latest` `7.1.51`, 152 characters | `/gi/7.1.51/en/character.json` was 404. `api.hakush.in` did not resolve |
| 2026-10-11 | `GET https://enka.network/api/uid/0/?info` | None | 400 | Documented bad-UID path. No real UID |
| 2026-10-11 | `GET` webstatic mi18n version URL from the local log | None | 200 `{"version":16}` | One file, not a site map |
| 2026-10-11 | `GET https://dispatchosglobal.yuanshen.com/query_region_list` | None | 200, 16-character body, not JSON | No extra parameters |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Interpretation</h2>

1. This PC has a current HoYoPlay-managed global Genshin install (7.1.0). On 2026-10-10 `HoYoProtect` was Stopped. On 2026-10-11 it was Running, demand start, while `GenshinImpact.exe` was running, and Stopped again at 17:40Z after the game exited while HoYoPlay was still open. The load was not watched, and the unload call was not traced.  
2. Telemetry-oriented launcher binaries are present and signed — consistent with privacy-policy expectations, not proof of malice.  
3. Wish history for community tools depends on an official-service `getGachaLog` surface that is **not** documented as a third-party developer API, plus temporary `authkey`s.  
4. Paimon.moe does **not** receive wishes automatically from the game. A history URL is pasted into the site. That URL (including `authkey`) is posted to Paimon’s `/corsproxy`, which fetches HoYoverse. Wish **list rows** may be enqueued to Redis `wish-vault`; **no consumer or job TTL was found in the reviewed public source** (production fate remains <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown">).  
5. Browser `localforage` holds pity/history objects; optional Drive sync uploads the full save JSON; optional tally submits aggregates **with `uid`** (not anonymous).  
6. genshin.dev, gi.yatta.moe, and static.nanoka.cc returned public static game data on 2026-10-11. Enka's documented error path returned 400 for UID 0. No real showcase was requested.  
7. During play, `GenshinImpact.exe` kept TCP `8.211.9.13:8999`, which is listed in a local security-server IP file on Alibaba address space. Other sockets were mostly CloudFront on port 443. Payloads and origin hostnames were not recovered.  
8. This remains a case study — **not** a completed security audit.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Follow-up</h2>

1. The 17:39Z exit sample is recorded: game and `ZFGameBrowser.exe` gone, `HoYoProtect` Stopped, `HYP.exe` and four `HYPHelper.exe` still present. A later launch can add the missing pre-launch sample.  
2. Re-verify Paimon importer hosts after major game patches.  
3. Compare privacy-policy updates when version date changes.  
4. Whether production runs a `wish-vault` worker. Live authkeys stay out of these notes.  
5. Do **not** add authkey-based live tests to these notes.  
6. Hostname attribution for the CloudFront sockets needs a DNS cache, SNI, or a proxy log. Do not decrypt traffic.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Non-claims</h2>

- Genshin/HoYoPlay is malware or proven safe  
- `HoYoProtect` idle state equals confirmed “on-demand only forever”  
- Wish API is officially supported for third parties  
- Paimon `wish-vault` jobs are permanently stored, shared, or unused in production  
- Signature validity equals trustworthiness of all behaviours  
- “Uses localforage” equals “data never leaves the device”  

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Case overview](README.md)
- [Developer features](developer-features.md)
- [API and endpoints](api-and-endpoints.md)
- [Evidence and confidence](../../methodology/evidence-and-confidence.md)
