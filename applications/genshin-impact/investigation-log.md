<h1 style="text-align:center">🕵️ Genshin Impact — investigation log</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Hands-on research log (2026-10-10 UTC). Paths and usernames sanitised. Wish URLs / authkeys / UIDs not published.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Summary</h2>

| Field | Value |
| --- | --- |
| **Status** | Hands-on case study — local inventory + line-cited Paimon source review |
| **OS** | Windows 11 Pro 64-bit 10.0.26300; PowerShell 5.1 |
| **Install** | HoYoPlay 1.18.0.380 + Genshin `hk4e_global` game_version 7.1.0 |
| **Processes** | No Genshin/HoYo processes at snapshot |
| **Driver** | `HoYoProtect` Stopped/Manual; sys file hash matched game copy |
| **API focus** | Wish `getGachaLog`, Paimon `/corsproxy` + vault, Enka, genshin.dev |

<h3 style="text-align:center">Explicitly not performed</h3>

- Launching HoYoPlay or Genshin Impact  
- Reading `output_log.txt` / webCaches / `UidInfo.txt`  
- Running `getlink.ps1` or importing wishes  
- Authenticated HoYoLAB or `getGachaLog` live calls  
- Installing or executing Paimon.moe / `paimon-moe-api` code  
- OAuth / Google account access  
- Debugger, memory inspection, packet capture, driver changes  

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Chronology](#chronology)
- [Commands executed](#commands-executed)
- [Live endpoint checks](#live-endpoint-checks)
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

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Interpretation</h2>

1. This PC has a current HoYoPlay-managed global Genshin install (7.1.0) with a registered but idle `HoYoProtect` driver.  
2. Telemetry-oriented launcher binaries are present and signed — consistent with privacy-policy expectations, not proof of malice.  
3. Wish history for community tools depends on an official-service `getGachaLog` surface that is **not** documented as a third-party developer API, plus temporary `authkey`s.  
4. Paimon.moe does **not** receive wishes automatically from the game; the user pastes a history URL. That URL (including `authkey`) is posted to Paimon’s `/corsproxy`, which fetches HoYoverse. Wish **list rows** may be enqueued to Redis `wish-vault`; **no consumer or job TTL was found in the reviewed public source** (production fate remains <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown">).  
5. Browser `localforage` holds pity/history objects; optional Drive sync uploads the full save JSON; optional tally submits aggregates **with `uid`** (not anonymous).  
6. genshin.dev / Enka provide public community data unrelated to HoYoverse’s wish API.  
7. This remains a case study / source review — **not** a completed security audit.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Follow-up</h2>

1. Optional: observe process/driver state **during** a user-initiated play session (user launches; agent only read-only lists).  
2. Re-verify Paimon importer hosts after major game patches.  
3. Compare privacy-policy updates when version date changes.  
4. Out-of-repo: whether production runs a `wish-vault` worker (ops/privacy disclosure), without using live authkeys in Fieldnotes.  
5. Do **not** add authkey-based live tests to Fieldnotes.

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
- [API and endpoints](api-and-endpoints.md)
- [Evidence and confidence](../../methodology/evidence-and-confidence.md)
