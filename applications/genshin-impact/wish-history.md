<h1 style="text-align:center">Genshin Impact — Wish History and account screens</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

What the Wish, character, and party screens show, how each change shows up on the PC, and where the trace is stored. The earlier source review of `getGachaLog` stays in [API and endpoints](api-and-endpoints.md).

Each checkpoint pairs the on-screen state with a read-only sample: process names and start times, established TCP connections, and `%USERPROFILE%\AppData\LocalLow\miHoYo\Genshin Impact\output_log.txt`. The game window is described only where a screenshot or the on-screen label is recorded. Port 443 does not reveal the URL inside the connection.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Session</h2>

| Item | Value | Status |
| --- | --- | --- |
| Date | 2026-10-11 | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Client | `game_version=7.1.0`, `wpf_version=7.1.0.48145775`, `channel=1`, `cps=pcgclid` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Active `config.ini` |
| Install id | `hk4e_global` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> HYP registry |
| In-game server label | Not printed on the Wish screens that were captured | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> A local file is named `os_euro`; that is not the on-screen server name |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">What these samples can show</h2>

| Visible from the PC | Not visible from these samples |
| --- | --- |
| Process names, start times, and image paths | Clicks, unless the screen itself is described |
| A process that starts or exits between two samples | HTTP method, URL path, or response body on port 443 |
| Established TCP: owner process, remote IP, port | A reliable hostname. The DNS client cache was empty, and TLS server names were not captured |
| File size and last-write time of `output_log.txt` | `authkey`, cookies, UID, or web-cache contents |

A connection that is already open before Wish History opens cannot be attributed to that screen.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 0 — before Wish History</h2>

**When:** 2026-10-11 17:09:36Z. Wish History was not open. HoYoPlay and the game had been running since 16:42:08Z.

| Process | Count | Started (UTC) |
| --- | --- | --- |
| `HYP.exe` | 1 | 16:42:08 |
| `HYPHelper.exe` | 4 | 16:42:09 |
| `GenshinImpact.exe` | 1 | 16:42:33 |
| `ZFGameBrowser.exe` | 6 | 16:44:43–16:44:46 |
| `HoYoProtect` | Running, Manual | Already loaded before this checkpoint |

Established remote TCP, all owned by `GenshinImpact.exe`:

| Remote | Note |
| --- | --- |
| `8.211.9.13:8999` | Same address as the earlier security-server list. Still up. Not new at this checkpoint |
| `18.165.160.33:443` | New CloudFront-range address relative to the 16:54Z sample. Not yet reverse-resolved at this checkpoint |
| `3.173.182.118:443` | Same. The earlier CloudFront peer was `3.173.182.102` |

No `HYUpdater.exe`, `crashreport.exe`, or `upload_crash.exe`.

**This checkpoint establishes** the process set and three remote sockets that are already present before Wish History is opened.

**It does not establish** what the Wish screen shows, or that any of these sockets serves wish records.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 1 — Wish screen open</h2>

**On screen:** the wishing page. **Sample:** 2026-10-11 17:11:01Z, about a minute and a half after the baseline.

| Check | Result | Status |
| --- | --- | --- |
| Process set | Same 12 processes, same pids. No new browser, updater, or crash process | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> No process start for opening the Wish screen |
| Remote TCP | Same three sockets, all `GenshinImpact.exe`: `8.211.9.13:8999`, `18.165.160.33:443`, `3.173.182.118:443` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> No new established remote at sample time |
| `ZFGameBrowser.exe` | Still the six processes from 16:44Z. No TCP of their own | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `output_log.txt` | Rewritten 17:10:41Z, size 343,443 bytes. Last 80 KB contained the same `sdk.hoyoverse.com` payment, announcement, and service-worker paths already seen earlier. Word search for gacha / wish / banner / `getGacha`: **0 hits** | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> The log changed. That tail has no history URL |
| Game working set | About 4,337 MB at 17:09Z, about 4,623 MB at 17:11Z | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> The number changed. Cause is <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **unknown** |

Opening the Wish screen did not add a process or a new established connection in this sample. The recent log tail has no wish-history URL. A connection that opened and closed between the two samples would have been missed. The banner artwork may be data the client already had. That is <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **plausible**, not shown.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 2 — Wish History open</h2>

**On screen:** Wish History. **Sample:** 2026-10-11 17:12:37Z.

| Change since 17:11:01Z | Result | Status |
| --- | --- | --- |
| New process | `ZFGameBrowser.exe` pid 21964, started **17:12:17Z**. Parent not re-queried. No TCP of its own in this sample | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> The start time matches the log write |
| Browser connections | Existing `ZFGameBrowser.exe` pid 21992, started 16:44:44Z, now has three established sockets on port 443. At the Wish-screen sample this process had none | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Game sockets | Unchanged: `8.211.9.13:8999`, `18.165.160.33:443`, `3.173.182.118:443` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Launcher sockets | `HYP.exe` and one `HYPHelper.exe` gained several new port-443 sockets in the same minute. Their PTRs are CloudFront | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Present. Not attributed to History. The launcher can refresh on its own |
| `output_log.txt` | Written 17:12:18Z. The tail contains one history-page URL. `getGachaLog` and `public-operation` are absent from the last 150 KB | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |

History page recorded from the log, query **values** removed:

| Field | Value |
| --- | --- |
| Host | `gs.hoyoverse.com` |
| Path | `/genshin/event/e20190909gacha-df01aea2/index.html` |
| Parameter names | `win_mode`, `no_joypad_close`, `authkey_ver`, `sign_type`, `auth_appid`, `init_type`, `gacha_id`, `timestamp`, `lang`, `device_type`, `game_version`, `authkey`, `region` |

`authkey` is present as a parameter name. The value was not copied.

Browser peers at this sample:

| Remote | Attribution | Tied to History? |
| --- | --- | --- |
| `163.181.243.203:443` | No PTR. RIPE: AS24429, Zhejiang Taobao Network Co., Ltd, prefix `163.181.243.0/24` | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> Same process, same minute as the history URL. The IP was not resolved to `gs.hoyoverse.com` |
| Two `2600:9000::` addresses on port 443 | Same prefix that resolved to CloudFront earlier on 2026-10-11. These two addresses had no PTR in this lookup | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> CDN edges used by the browser process. Origin name not captured |

The history **page** is what the log shows. The separate `getGachaLog` request described by Paimon.moe was **not** in that log tail. It may be issued by the page after load and simply not written to `output_log.txt`. That remains <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **unknown** for opening History.

The logged URL, values that are not credentials:

| Parameter | Value |
| --- | --- |
| `auth_appid` | `webview_gacha` |
| `init_type` | `301` |
| `gacha_id` | `79a292d76f0cf33b9e30babec9fac01c1860d45d` |
| `lang` | `en` |
| `region` | `os_euro` |
| `game_version` | `OSRELWin7.1.0_R48652077_S48511369_D48533839` |

`301` is the character-event type used by the Paimon importer. That match is <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> source plus this URL. `authkey` is still withheld.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 3 — Novice Wishes on screen</h2>

**On screen:** Character Event Wish and Character Event Wish 2 had already been shown. At 17:14 UTC the list was **Novice Wishes**. Item name, rarity, and date were not visible on that empty list.

**Sample:** 2026-10-11 17:14:42Z, while that list was up.

| Check | Result |
| --- | --- |
| New browser process | None. Still the seven `ZFGameBrowser.exe` processes, the newest from 17:12:17Z |
| Browser TCP | Pid 21992 still on port 443. The Taobao address changed from `163.181.243.203` to `163.181.243.202` (same `/24`). The two CloudFront-prefix IPv6 addresses remained |
| `output_log.txt` | Unchanged since 17:12:18Z (347,044 bytes). Still one `gs.hoyoverse.com` history URL, `init_type=301`. `getGachaLog` still absent |

Novice Wishes was visible and the game log did not gain a second history URL or a `getGachaLog` line. Changing banner is therefore not recorded in `output_log.txt`. The list can be coming from data already held by the history page, or from a request on the existing browser connection that this log does not echo. Which of those is true is <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **unknown**. The encrypted sockets do not show a URL.

Novice Wishes has **no rows**: that banner has never been used. The empty list is the history UI for an unused banner. It does not show item name, rarity, or date, so those columns stay <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **unknown** from this screen.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 4 — Weapon Event Wish with rows</h2>

**On screen:** the Wish Type control was **Weapon Event Wish**, and the list had rows. Novice Wishes, shown just before, was empty because that banner has never been used.

**Sample while that weapon list was up:** 2026-10-11 17:18:16Z. Same result as 17:17:01Z.

| Check | Result |
| --- | --- |
| Browser processes | Same seven. None started after 17:12:17Z |
| Browser TCP | Still one socket, `163.181.243.197:443` on pid 21992 |
| `output_log.txt` | Still 347,044 bytes, last write 17:12:18Z, `init_type=301` only. No `getGachaLog` |

Weapon Event Wish was on screen and the log did not record a new page or a new banner type. Banner switches, including an empty Novice list and a populated Weapon list, stay inside the history page opened at 17:12Z.

<h3 style="text-align:center">What the window shows</h3>

Screenshot of the history window. Individual pull rows are not listed.

| UI element | Shown |
| --- | --- |
| Title | Wish History |
| Banner control | A Wish Type dropdown. Selected value: Weapon Event Wish |
| Columns | Item Type, Item Name, Wish Type, Time Received |
| Rarity or star column | Not present |
| Row contents | Item Type was Weapon. Item Name was a weapon name. Wish Type repeated Weapon Event Wish. Time Received was a date and time |
| Pagination | Page 1, with previous and next controls. More than one page exists; the next page was not opened |
| Empty banner | Novice Wishes showed no rows |

The help text above the table says, in paraphrase: history covers about the past year; records appear about an hour after the wish; displayed times use server time; server zones listed include Europe (UTC+1), which matches `region=os_euro` on the history page URL. No UID was visible.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 5 — Chronicled Wish and Lightrace Wish</h2>

**On screen:** one Wish Type entry, **Chronicled Wish and Lightrace Wish**, with rows. It is not two separate dropdown values.

**Sample while that list was opening:** 2026-10-11 17:19:07Z.

| Check | Result |
| --- | --- |
| Browser processes | Same seven |
| Browser TCP | Still `163.181.243.197:443` on pid 21992 |
| `output_log.txt` | Still 347,044 bytes, last write 17:12:18Z, `init_type=301` only. No `getGachaLog` and no `public-operation` host |

The combined banner was on screen and the logged history URL did not change.

The columns are the same four. On this page, weapon rows used Item Type Weapon and a weapon name with no star marker. A character row used Item Type Character, and the item name included a star count in the name itself, such as a `1-Star` suffix. The Wish Type cell on those rows read **Lightrace Wish**, which is narrower than the dropdown label. Page 1 was showing, with next-page controls. Personal rows are not listed here.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 6 — page 2</h2>

**On screen:** page 2 of Chronicled Wish and Lightrace Wish, after one click of the next-page control.

**Baseline immediately before the click:** 2026-10-11 17:20:39Z. Seven `ZFGameBrowser.exe` processes, one browser socket `163.181.243.197:443` on pid 21992, `output_log.txt` 347,044 bytes last written 17:12:18Z.

**Sample with page 2 showing:** 2026-10-11 17:21:02Z.

| Check | Result |
| --- | --- |
| HoYo processes | Same set. `HYP.exe` 16:42:08Z, four `HYPHelper.exe` 16:42:09Z, `GenshinImpact.exe` 16:42:33Z, seven `ZFGameBrowser.exe` with the newest still 17:12:17Z. No updater or crash process |
| Browser TCP | Same remote, pid 21992 to `163.181.243.197:443`. That socket’s creation time is **17:19:06Z**, before the baseline and before the click |
| `output_log.txt` | Still 347,044 bytes, last write 17:12:18Z. No new history URL and no `getGachaLog` |

Page 2 did not start a process, replace the browser connection, or write the game log. A request carried inside the TLS session that was already open would not show up in this sample. That remains <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **unknown**. What paging rules out is a new connection and a new line in `output_log.txt`.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 7 — Permanent Wish</h2>

**On screen:** the Wish Type control was **Permanent Wish**, and the list had rows. This is the last banner checked.

**Sample as the Wish Type changed:** 2026-10-11 17:22:12Z. **Recheck once the screenshot showed Permanent Wish:** 2026-10-11 17:22:52Z. Both samples match.

| Check | Result |
| --- | --- |
| HoYo processes | Same set as checkpoint 6. Seven browser processes; the newest still 17:12:17Z |
| Browser TCP | Still pid 21992 to `163.181.243.197:443`, created 17:19:06Z |
| `output_log.txt` | Still 347,044 bytes, last write 17:12:18Z, `init_type=301` only. No `getGachaLog` |

Permanent Wish was on screen and the logged history URL did not change. It matches Novice, Weapon Event, and Chronicled and Lightrace: banner changes stay on the history page opened at 17:12Z.

The columns are the same four. Weapon rows used Item Type Weapon and Wish Type **Permanent Wish**. A character row used Item Type Character, with a star count in the item name, such as a `1-Star` suffix. Page 1 was showing, with next-page controls. Personal rows are not listed here.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 8 — character screen</h2>

**On screen:** Wish History had been closed and the character screen was open. No screenshot at this sample, so the character, level, and party are not recorded here.

**Sample:** 2026-10-11 17:23:24Z.

| Check | Result |
| --- | --- |
| History webview | `output_log.txt` grew from 347,044 to 347,617 bytes, last write 17:23:10Z. New lines are a webview hide, `OnGetWebViewPageClose`, `OnClose`, `Browser OnDestroy`, and `SetShadowQuality Finish`. No character, avatar, or player URL. No `getGachaLog` |
| Browser processes | The history-era `ZFGameBrowser.exe` pid 21964 (started 17:12:17Z) exited. Pid 14932, from the original 16:44Z set, also exited. A new `ZFGameBrowser.exe` pid 17788 started 17:23:08Z and had no TCP of its own |
| History socket | Pid 21992 still held `163.181.243.197:443`, created 17:19:06Z, after the webview destroy line |
| Game TCP | `8.211.9.13:8999` still up, created 16:44:05Z. A new `18.165.160.41:443` was created 17:23:15Z. The earlier game peers `18.165.160.33:443` and `3.173.182.118:443` were gone |
| Launcher TCP | `HYP.exe` had `3.162.20.89:443`, created 17:22:12Z, before this screen. Not attributed to the character screen |

Log timestamps in this tail are eight hours ahead of UTC. `01:23:08` in the file is the same instant as the process start at 17:23:08Z.

Leaving Wish History destroyed that webview and started a browser process with no connection of its own. The character screen did not write an account or character endpoint into `output_log.txt`. The new game connection is in the same `18.165.160.0/24` prefix as an earlier CloudFront-range peer. It appeared in the same minute as this screen. That timing is <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **plausible** for a content fetch, and it is not a captured URL. CloudFront peers had already changed earlier in play, before this screen.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 9 — character on screen, before a switch</h2>

**On screen:** the character screen showed **Columbina**, labeled Hydro, on the Attributes page, level 90. Attribute numbers, friendship, and the profile text are not copied.

**Log check:** 2026-10-11 17:24:54Z. `output_log.txt` was still 347,617 bytes, last write 17:23:10Z, ending at `SetShadowQuality Finish`. A full-file search found **0** matches for Columbina, avatar, character, Hydro, or Nod-Krai. The 40 `Player` hits are earlier scene-loading lines (`PlayerModule`, `OnPlayerEnterSceneNotify`), not this menu.

The on-screen character is not in the game log. There is no log entry for this screen to match the screenshot against.

**Baseline before the next character change**, same sample:

| Check | Result |
| --- | --- |
| Processes | Same 12 as checkpoint 8, including `ZFGameBrowser.exe` pid 17788 from 17:23:08Z |
| Game TCP | Unchanged: `8.211.9.13:8999` and `18.165.160.41:443` |
| History socket | The `163.181.243.197:443` socket on pid 21992 had closed since 17:23:24Z |
| Launcher socket | The `3.162.20.89:443` socket on `HYP.exe` had also closed |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 10 — switched to Flins</h2>

**On screen:** the character screen changed from Columbina to **Flins**.

**Sample:** 2026-10-11 17:25:58Z, against the 17:24:54Z baseline.

| Check | Result |
| --- | --- |
| Processes | Same 12. No new browser process |
| Game TCP | Same two sockets, same creation times: `8.211.9.13:8999` from 16:44:05Z and `18.165.160.41:443` from 17:23:15Z |
| `output_log.txt` | Grew from 347,617 to 347,683 bytes, last write 17:25:46Z. The added lines are `submit shader load 3`, then `submit shader load 2` twice. They have no timestamps of their own |
| Names | Full-file search: Flins 0, Columbina 0, avatar 0, character 0 |

Changing character did not open a connection and did not write either name. The new log text is a shader-load note. A local model swap causing that line is <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **plausible**. It is not a character or account request.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 11 — party screen</h2>

**On screen:** Party Setup. The four slots, left to right, were Flins level 90, Ineffa level 90, Clorinde level 60, and Columbina level 90. A Team Bonus control was visible. Outfits are not described.

**Sample while that screen was opening:** 2026-10-11 17:27:11Z, against the 17:25:58Z Flins baseline.

| Check | Result |
| --- | --- |
| Processes | Same 12. No new browser process |
| Game TCP | `8.211.9.13:8999` and `18.165.160.41:443` still up. A new `3.173.182.11:443` was created 17:26:55Z. That address is in the same `/24` as earlier CloudFront-range peers `3.173.182.102` and `3.173.182.118` |
| `output_log.txt` | Grew from 347,683 to 347,780 bytes, last write 17:26:59Z. Added lines: `submit shader load 2`, then `[SetShadowQuality Finish]` at log time 01:26:59, then `submit shader load 2` again |
| Names | Full-file search: Flins 0, Ineffa 0, Clorinde 0, Columbina 0. The one `party` hit is `ThirdpartyLoginManager initialize success` at log time 00:43:16, from startup, not this screen |

The party on screen is not in the game log. Opening Party Setup wrote the same kind of shadow and shader lines as the character screen, and added one CloudFront-range connection. No party or team URL was logged. The connection’s host is <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **unknown**.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 12 — Clorinde replaced by Sucrose</h2>

**On screen:** Clorinde was removed from the party and Sucrose was added. About nine minutes passed after checkpoint 11 before this sample, so only a log timestamp at the swap can be tied to it.

**Sample:** 2026-10-11 17:36:56Z.

| Check | Result |
| --- | --- |
| Processes | Same 12 |
| Game TCP | Only `8.211.9.13:8999`, still created 16:44:05Z. The `18.165.160.41:443` and `3.173.182.11:443` sockets from checkpoint 11 were gone. No new socket was established at the swap |
| `output_log.txt` | Grew from 347,780 to 347,889 bytes, last write 17:36:43Z. The appended text is one untimestamped `submit shader load 2`, then `[VCHumanoidMove] AO config not found for configId: 10000043` at log time 01:36:43 |
| Names | Sucrose 0, Clorinde 0. The `party` hit is still the startup `ThirdpartyLoginManager` line |

`10000043` is Sucrose in the public avatar record `gi.yatta.moe/api/v2/en/avatar/10000043` (Anemo, 4-star), checked at this sample. `genshin.jmp.blue/characters/sucrose` names Sucrose and does not use that numeric id. The log line does not contain the name. The id plus the timestamp matching the swap is <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> **strongly supported** as the character who was added. Clorinde’s name and id were not written.

The line is a local humanoid-move config miss, not a URL. A party update inside the existing port 8999 session would not be visible here. No such payload was captured.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 13 — back in the world</h2>

**On screen:** Party Setup was closed. The game was back in the world.

**Sample:** 2026-10-11 17:39:00Z, against the 17:36:56Z party-edit sample.

| Check | Result |
| --- | --- |
| Processes | Same 12 |
| Game TCP | Still only `8.211.9.13:8999`, created 16:44:05Z. No new socket |
| `output_log.txt` | Still 347,889 bytes, last write 17:36:43Z. The tail is still the Sucrose `configId: 10000043` line |

Leaving Party Setup did not write a log line and did not open a connection. The party edit’s only logged trace remains the local config lookup from checkpoint 12.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Checkpoint 14 — game closed</h2>

**On screen:** the game was closed. HoYoPlay was still open.

**Sample:** 2026-10-11 17:39:56Z for processes, 17:40:08Z for the driver.

| Check | Result |
| --- | --- |
| Game and browser | `GenshinImpact.exe` and every `ZFGameBrowser.exe` were gone. No `HYUpdater.exe`, `crashreport.exe`, or `upload_crash.exe` |
| Launcher | `HYP.exe` pid 16456 and the same four `HYPHelper.exe` processes from 16:42Z were still running |
| `HoYoProtect` | **Stopped**, start mode Manual (demand start). `sc.exe query` also reported Win32 exit code 31 (`0x1f`). The driver was not running. That exit code was not investigated further |
| Port 8999 | No remaining connection to `8.211.9.13:8999` |
| `output_log.txt` | Grew from 347,889 to 348,398 bytes, last write 17:39:40Z. New lines are `Call QuitApplication` twice, `On Application quit`, `HoYoPass_Uninitialize call success`, `Application will quit.`, `OnApplicationQuit`, a shutdown destroy with `threw=False`, and `zfb_shutdown` |
| Launcher TCP | In the few seconds after the quit line, `HYP.exe` and one `HYPHelper.exe` opened new port-443 sockets. One remote was `163.181.243.199`, the same `/24` as the history-page browser. Others were CloudFront-prefix addresses, including `2600:9000`. Hostnames were not captured |

The game process exited and `HoYoProtect` was Stopped while the launcher was still running. Which process issued the unload was not traced. The stop coinciding with game exit, with the launcher still up, is <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> **strongly supported**. The quit log has no wish, character, or party URL.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [API and endpoints](api-and-endpoints.md)
- [Developer features](developer-features.md)
- [Investigation log](investigation-log.md)
