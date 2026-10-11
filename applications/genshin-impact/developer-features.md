<h1 style="text-align:center">Genshin Impact — developer features</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Catalogue of interfaces that could feed community tools. Machine-readable copy: [developer-features.yaml](developer-features.yaml). Wish-import internals stay in [API and endpoints](api-and-endpoints.md).

Checked 2026-10-11. Each entry is one of:

| Status | Meaning |
| --- | --- |
| **Verified integration** | Tested with an ordinary read inside its permitted access model |
| **Documented integration** | A reliable description exists; a successful data read was not completed on 2026-10-11 |
| **Potential integration** | Something suggests the interface is useful; behaviour or permission is not established |
| **Unavailable or restricted** | The access needed was not granted, the host did not resolve, or the interface is not usable as an open API |

A reachable response is not permission to redistribute it.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">What is actually usable</h2>

No official HoYoverse game-data developer API was identified. The interfaces that returned useful public data are community static catalogues.

| Interface | Status | What a tool could do with it |
| --- | --- | --- |
| [genshin.jmp.blue](https://genshin.jmp.blue/) | Verified | Character, weapon, artifact, enemy, and material pages |
| [gi.yatta.moe/api/v2](https://gi.yatta.moe/api/v2/en/avatar) | Verified | Amber-style character index (136 items in this check) |
| [static.nanoka.cc](https://static.nanoka.cc/manifest.json) | Verified | Versioned character JSON (152 entries for `7.1.51`) plus a manifest |
| Enka `/api/uid/{uid}/` | Documented | Showcase builds, only for a UID the caller is allowed to query |
| `getGachaLog` | Restricted | Wish history, only with the account holder's own short-lived `authkey` |
| HoYoLAB record modules | Restricted | Resin, diary, and chronicle data, only with that account's cookies |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Verified</h2>

<h3 style="text-align:center">genshin.dev</h3>

Fan-made static API. The project states that it does not talk to the game. `GET /` returned `application/json` and ten type names: `artifacts`, `boss`, `characters`, `consumables`, `domains`, `elements`, `enemies`, `materials`, `nations`, `weapons`.

`GET /characters/ayaka?lang=en` returned a character document. Fields included `name`, `title`, `vision`, `weapon`, `nation`, `rarity`, talents, constellations, and ascension materials. Sanitised excerpt:

```json
{"name":"Kamisato Ayaka","title":"Frostflake Heron","vision":"Cryo","weapon":"Sword","rarity":5}
```

Optional query `lang`. No authentication.

<h3 style="text-align:center">Project Amber via gi.yatta.moe</h3>

`ambr-py` sets its base URL to `https://gi.yatta.moe/api/v2`. `GET /api/v2/en/avatar` returned JSON with `response` 200 and `data.items`. This check counted **136** items. Item `10000002` was Kamisato Ayaka, with fields `id`, `rank`, `name`, `element`, `weaponType`, `region`, `icon`, `birthday`, `release`, and `route`.

`api.ambr.top` did not resolve from this network. That hostname is <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **unavailable here**, which does not retire the Amber data set served by `gi.yatta.moe`.

<h3 style="text-align:center">Hakushin static files</h3>

`hakushin-py` uses `https://static.nanoka.cc`, not `api.hakush.in`. The latter name did not resolve.

| Request | Result |
| --- | --- |
| `GET /manifest.json` | 200 JSON. Keys `gi`, `hsr`, `ww`, `zzz`, `nte`. For `gi`, `latest` was `7.1.51`, `live` was `7.1`, and `available` included `7.1` |
| `GET /gi/7.1.51/character.json` | 200 JSON, **152** entries |
| `GET /gi/7.1.51/en/character.json` | **404** |

The list URL matches `fetch_characters` in the wrapper (`/{game}/{version}/character.json`). Detail URLs add a language segment and an id. Those detail URLs were not fetched.

The installed client reports `game_version=7.1.0`. The manifest string `7.1.51` is a data-pack version, not that config field.

<h3 style="text-align:center">webstatic version file</h3>

The live game log contained:

`https://webstatic.hoyoverse.com/admin/mi18n/plat_os/m09291531181441/m09291531181441-version.json`

One GET returned `{"version":16}`. That confirms this particular static file. It does not map the rest of `webstatic.hoyoverse.com`.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Documented, not fully tested</h2>

**Enka.Network** documents `GET https://enka.network/api/uid/{uid}/` and `?info`. The docs forbid UID enumeration, ask for a custom `User-Agent`, and tell clients to honour `ttl` and HTTP 429. This session sent one request, `GET /api/uid/0/?info`, with a custom user agent. The response was **400**, which the docs define as a wrong UID format. No real player's showcase was requested.

Profile routes such as `/api/profile/{username}/hoyos/` are in the same document. They were not called.

**Hakushin character detail** is constructed in source as `/gi/{version}/{lang}/character/{id}.json`. Not fetched.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Potential</h2>

| Candidate | Why it is only potential | What was not done |
| --- | --- | --- |
| `dispatchosglobal.yuanshen.com/query_region_list` | The path is in `output_log.txt`. A bare GET returned HTTP 200 and a 16-character body (`text/html`), not a JSON region list. Decoded length 11 bytes. Format left inconclusive | No parameter guessing |
| `log-upload-os.hoyoverse.com/sdk/dataUpload` | Present in `launcher.log` | No POST |
| `sdk.hoyoverse.com` announcement and payment pages | Paths present in `output_log.txt` | Those pages were not fetched |
| Hosts inside `HoYoDeviceFpSDK.dll` | Embedded strings, including `sg-public-data-api.hoyoverse.com` and several `pre-` / `testing-` names | Not requested. Strings are not proof of a connection |
| Launcher CDN `hyp-webstatic.hoyoverse.com` and `launcher-webstatic.hoyoverse.com` | Present in `launcher.log` | Not fetched. Not bound to a specific socket |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Unavailable or restricted</h2>

| Interface | Why it is in this group |
| --- | --- |
| Official public game-data API | None identified in the public materials reviewed |
| `getGachaLog` | Official wish-history service. Needs `authkey`. Not a published third-party API. Not called. The history **page** on `gs.hoyoverse.com` was seen in the local log on 2026-10-11; the API path was not |
| HoYoLAB game records on `sg-public-api.hoyolab.com` | Third-party wrappers describe cookie-authenticated modules. Cookies were not used. Exact paths were not re-tested |
| `api.ambr.top` | DNS failure on 2026-10-11 |
| `api.hakush.in` | DNS failure on 2026-10-11 |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Link to the local session</h2>

| Local observation | Catalogue entry | What the link does **not** prove |
| --- | --- | --- |
| `output_log.txt` contains `dispatchosglobal.yuanshen.com/query_region_list` | Dispatch row above | That a captured TCP socket was that host. DNS cache was empty and TLS names were not read |
| `output_log.txt` contains the webstatic version URL | webstatic row | That every `webstatic` connection is this file |
| Game TCP to `8.211.9.13:8999` matches a local IP list | No public HTTP API | The port is not one of the HTTP catalogues above |
| CloudFront addresses on port 443 | Launcher CDN hosts are only a plausible overlap | SNI was not captured, so the origin name is unknown |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Live GETs on 2026-10-11, recorded in the [investigation log](investigation-log.md)
- [genshindev/api](https://github.com/genshindev/api)
- [seriaati/ambr](https://github.com/seriaati/ambr) (`BASE_URL` `https://gi.yatta.moe/api/v2`)
- [seriaati/hakushin-py](https://github.com/seriaati/hakushin-py) `clients/base.py` and `clients/gi.py`
- [EnkaNetwork/API-docs api.md](https://github.com/EnkaNetwork/API-docs/blob/master/api.md)
- [hoyoapi module docs](https://vermaysha.github.io/hoyoapi/docs/api/modules)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [API and endpoints](api-and-endpoints.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [developer-features.yaml](developer-features.yaml)
