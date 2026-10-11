<h1 style="text-align:center">Genshin Impact — privacy and telemetry</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Vendor disclosures plus local artefacts that suggest telemetry/security tooling. Filenames and signed libraries are **not** packet proofs.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [Vendor disclosures](#vendor-disclosures)
- [Local telemetry-related artefacts](#local-telemetry-related-artefacts)
- [Anti-cheat / security surface](#anticheat--security-surface)
- [Wish-history and third-party tools](#wish-history-and-third-party-tools)
- [Connections during play (2026-10-11)](#connections-during-play-2026-10-11)
- [Local files written around launch](#local-files-written-around-launch)
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
| Connection metadata from this PC during one play session | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> IPs, ports, and owner pids. Payloads remain <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **unknown** |

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
| Driver was Running, demand start, while the game process existed on 2026-10-11, and Stopped at 17:40Z after exit while HoYoPlay remained | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> State only. Behaviour inside the driver was not traced |

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

No live `authkey` was extracted or tested.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Connections during play (2026-10-11)</h2>

Three samples between about 16:45Z and 16:54Z. `Get-DnsClientCache` returned **zero** entries, so a socket could not be joined to a DNS name. Reverse DNS was used instead. Local addresses are omitted. TLS payloads and SNI were not captured.

| Owner | Remote | When seen | What that supports |
| --- | --- | --- | --- |
| `GenshinImpact.exe` | `8.211.9.13:8999` TCP established | All three samples | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Same address is the first entry in `%LOCALLOW%\miHoYo\Genshin Impact\security_server_default_iplist_os_euro.txt`, written 16:44Z. File contents: `8.211.9.13`, `47.91.86.250`, `8.211.3.101`. RIPE prefix overview: all three are AS45102, Alibaba (US) Technology Co., Ltd. The other two addresses were **not** connected in these samples. Port 8999 had no PTR |
| `GenshinImpact.exe` | `3.173.182.102:443` | All three samples | PTR `server-3-173-182-102.man51.r.cloudfront.net` |
| `GenshinImpact.exe` | `18.165.160.84:443` and `18.165.160.110:443` | Present in some samples, not all | CloudFront PTRs. `18.165.160.84` was also owned by `HYP.exe` in the first sample |
| `GenshinImpact.exe` | `18.66.118.4:443`, `3.33.237.18:443` | First sample only | CloudFront PTR, and `a32121eaf9bb9ac32.awsglobalaccelerator.com` (AS16509, Amazon). Gone in later samples |
| `GenshinImpact.exe` | `127.0.0.1` ports `50244` and `50245` | All samples | Both ends owned by the game process. Local IPC. No remote party |
| `HYP.exe` | `18.165.160.84:443`, `18.172.88.79:443`, and a `2600:9000::` address on 443 | First sample | CloudFront. Not present as established sockets in the last sample |
| One `HYPHelper.exe` | `18.165.160.41:443`, `18.66.118.7:443`, `3.165.148.47:443`, a `2600:9000::` address, and `2a00:1450:4009:c15::8a:443` | First sample | CloudFront PTRs, plus Google PTR `uv-in-f138.1e100.net`. The other three helpers had no established TCP in that sample |
| Six `ZFGameBrowser.exe` | None | After 16:44:43Z | No TCP rows for those pids. A short connection could have been missed |

UDP: `GenshinImpact.exe` had one local UDP bind (`0.0.0.0`, ephemeral port). `Get-NetUDPEndpoint` does not show a remote peer. A remote UDP flow was **not observed**.

| Layer | Statement |
| --- | --- |
| Observed | Owner pid, remote IP, port, TCP state, and PTR where one existed |
| Associated provider | CloudFront, Amazon Global Accelerator, Google, or Alibaba, from PTR or RIPE ASN |
| Inferred purpose | The `8999` peer is on the client's own "security server" IP list for `os_euro`. CloudFront 443 is consistent with CDN or web UI, including launcher static hosts found in logs, and was **not** pinned to one name |
| Confirmed by a vendor document | The privacy policy describes device, game, and security-related collection. It was not used to name these IPs |
| Unknown | Bytes sent, account fields, and the hostname behind each CloudFront or Accelerator address |

`output_log.txt` was locked by the game. A shared read extracted URL hosts only. Paths present on 2026-10-11 included `dispatchosglobal.yuanshen.com/query_region_list`, several `sdk.hoyoverse.com` payment and announcement pages, and one `webstatic.hoyoverse.com` i18n version file. Those are log strings. They were not matched to sockets.

A bare `GET` of the dispatch URL returned HTTP 200 and a 16-character body, not JSON. See [developer features](developer-features.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Local files written around launch</h2>

Under `%LOCALLOW%\miHoYo\Genshin Impact\`:

| File | What was done | What it supports |
| --- | --- | --- |
| `info.txt` | Read, then redacted | Written at 16:42:59Z. Fields include client version `7.1_rel`, OS, a device-model string, CPU, GPU, a numeric memory field, a UID, and a client IP. The file's own type line is `Windows Crash Release`. UID and IP are **not** published. The `Crashes` directory contained **zero** files, so this info file is not evidence that the game crashed on 2026-10-11 |
| `security_server_default_iplist_os_euro.txt` | Read | Three public IPs, as above. Written 16:44Z |
| `UidInfo.txt` | Existence and size only (11 bytes) | Not opened |
| `output_log.txt` | Hostnames only | Updated during play (about 311 KB). Full text not published |
| `LocalLog.log` | Hostname scan | No `http` URLs with HoYoverse hosts in the scan. Updated during play |
| webCaches `2.48` through `2.55` | Directory names only | Chromium-style cache trees. Local Storage and Session Storage were not opened |

`launcher.log` (last write 2026-10-11; the file is about 4 MB and can contain older lines) includes, in the 16:42Z startup block:

- Product display name HoYoPlay
- `https://launcher-webstatic.hoyoverse.com/...` as a Win7 patch URL
- `https://cs.hoyoverse.com/static/mihoyo-new-csc-service-hall-fe/index.html` as a safe-mode FAQ
- Updater `AppId` `604565`
- An updater `AppKey` (64 hexadecimal characters). **Not published**
- The text `Begin to start data upload and kibana report`
- A `CreateProcess` of `HYSafeMode.exe`

Elsewhere in that log, hostname extraction also found `hyp-webstatic.hoyoverse.com` and `log-upload-os.hoyoverse.com/sdk/dataUpload`. The log line establishes that a data-upload code path logged its start. It does not establish the payload or a completed HTTP transaction.

`upload_err.log` in the game folder is older (2026-03-21) and contains a completed upload note with HTTP `200`. That is a historical local result, not from 2026-10-11.

ASCII scans of signed DLLs found these host strings. Certificate, DoH, and Chromium word-fragments from the same scan are treated as noise and are not listed as destinations.

| Binary | Host strings retained | Label |
| --- | --- | --- |
| HoYoPlay `HoYoDeviceFpSDK.dll` | `sg-public-data-api.hoyoverse.com`, `public-data-api.mihoyo.com`, plus `pre-`, `testing-`, and `devop-` names on `hoyoverse.com` / `mihoyo.com` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Strings exist. <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Whether any were contacted |
| HoYoPlay and game `telemetry.dll` | `minor-api.mihoyo.com`, `minor-api-os.hoyoverse.com` | Same distinction |
| `MiHoYoMTRSDK.dll`, `HoYoNetworkSDK.dll`, `gmesdk.dll` | No HoYoverse hostname in this scan (certificate hosts only) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for the negative scan of those files. Not proof the modules never use a hostname built at runtime |

`HKCU\Software\miHoYo\Genshin Impact` holds Unity graphics preferences and many `MIHOYOSDK_*` value names, including a device-fingerprint value and a last-UID value. Those values were not read. Some value **names** embed an account id; the names are not reproduced.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">What was not verified</h2>

- Which CloudFront distribution each 443 socket used
- Payload of the `8999` session, or of `telemetry.dll` / the MTR SDK
- Whether the APM switches in HoYoPlay `config.ini` can be turned off in the UI
- Web-cache and `UidInfo.txt` contents
- Whether any in-game privacy toggle changes the connections above
- Driver and process state after the game exits  

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
