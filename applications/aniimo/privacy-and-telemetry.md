<h1 style="text-align:center">Aniimo — privacy and telemetry</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Aniimo](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Separates **what was on disk**, **what the process connected to**, **what a vendor policy says**, and **what is still a hypothesis**. No claim that Aniimo is malicious, and no claim that the analytics code was idle.

Authoritative chronology: [investigation log](investigation-log.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">🔎 Observations (this installation)</h2>

| Observation | Confidence |
| --- | --- |
| Four `AF_uuid_*` / `AF_counter_*` values under the current-user `Run` key | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| `AF_counter_4126040` read as `49` on 2026-10-11 (earlier notes recorded `48`); `AF_counter_759396` still `0` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> The step from 48 was not bracketed by a pre-launch read |
| `FPX.dll` and `FPXSteam.dll` contain `AF_uuid`, `AF_counter`, `SOFTWARE\Microsoft\Windows\CurrentVersion\Run`, and `https://events.appsflyer.com/...` URLs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> ASCII strings in those files |
| Version resource on those DLLs names FunPlus; Authenticode signer is Pawprint | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Exported-style names include `FPXSteam_AppsFlyer_Init`, `FPXSteam_AppsFlyer_Start`, `FPXSteam_AppsFlyer_LogEvent`, `FPXSteam_AppsFlyer_LogNewUser` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Strings, not a live debugger trace |
| Request templates in the same DLLs include field names such as `steamid`, `ip`, `customer_user_id`, `device_os_version`, `device_model`, and `device_ids` with types `AF_uuid` / `AF_counter` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> as template text. Population and transmission are <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** |
| TCP snapshots of `Aniimo.exe` did not include addresses resolved for `events.appsflyer.com` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> for those snapshots only |
| CrashSight log for this PID names `pc.crashsight.wetest.net` at 17:15:16 +01 | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> log text. The resolved address was not in the TCP snapshots |
| `%LOCALAPPDATA%\appsflyer` has two small files last written in December 2024; their bytes did not contain the current `Run`-key UUID strings | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Not attributed to this launch |
| `%ProgramData%\funpluspcsdk\PCSDK\worldx.global.prod\` cache file written 17:15:51 +01 | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> A FunPlus SDK cache changed during startup. Contents not parsed |
| Literal `4126040` and `759396` inside the searched game binaries | Not found |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">What the SDK strings establish</h2>

`FPX.dll` (version `1.0.0.1392`, product “FPX Game SDK”) and `FPXSteam.dll` (version `1.0.0.20`, product “FPXSteam Plugin”) contain the same AppsFlyer host with these paths:

```text
https://events.appsflyer.com/v1.0/c2s/session/app/steam/
https://events.appsflyer.com/v1.0/c2s/first_open/app/steam/
https://events.appsflyer.com/v1.0/c2s/inapp/app/steam/
https://events.appsflyer.com/v1.0/c2s/session/app/nativepc/
https://events.appsflyer.com/v1.0/c2s/first_open/app/nativepc/
https://events.appsflyer.com/v1.0/c2s/inapp/app/nativepc/
```

Nearby strings include `Error opening key`, `Error setting key`, and the `Run` key path. Log-style strings describe AppsFlyer init, start, new-user, and event calls, including the case where a dev key or app id is missing. No dev key was extracted or copied into these notes.

> **🔍 Finding**
>
> For this installation, AppsFlyer is no longer only a community guess about the `AF_*` names. The names, the registry path, and AppsFlyer’s event host are in the FunPlus SDK binaries that ship with Aniimo.

> **❓ Unverified**
>
> That does **not** prove the running process wrote `AF_uuid_4126040`, and it does **not** prove an HTTP request was made during this session. The app ids are not hardcoded in the files that were searched, which fits the SDK reading an id at runtime (`getAppID` appears in `FPXSteam.dll`) and also means `759396` cannot be blamed on a literal embedded in these DLLs.

`FPXGame.dll` did not match the AppsFlyer needles. `NEP2.dll` and `Aniimo.exe` did not either.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Endpoints</h2>

These are service hosts and addresses, not account credentials. Local paths, LAN addresses, GUIDs, and SDK secrets are omitted. A host listed as embedded was found as a string in a file. It was not assumed to be in use unless a connection row says so.

<h3 style="text-align:center">Embedded in the SDK binaries</h3>

| Endpoint | Build | Role of the string |
| --- | --- | --- |
| `https://events.appsflyer.com/v1.0/c2s/session/app/steam/` | Steam `FPX.dll`, `FPXSteam.dll` | AppsFlyer session |
| `https://events.appsflyer.com/v1.0/c2s/first_open/app/steam/` | Steam `FPX.dll`, `FPXSteam.dll` | AppsFlyer first open |
| `https://events.appsflyer.com/v1.0/c2s/inapp/app/steam/` | Steam `FPX.dll`, `FPXSteam.dll` | AppsFlyer in-app event |
| `https://events.appsflyer.com/v1.0/c2s/session/app/nativepc/` | Steam `FPX.dll` | AppsFlyer session, native PC path |
| `https://events.appsflyer.com/v1.0/c2s/first_open/app/nativepc/` | Steam `FPX.dll` | AppsFlyer first open, native PC path |
| `https://events.appsflyer.com/v1.0/c2s/inapp/app/nativepc/` | Steam `FPX.dll` | AppsFlyer in-app, native PC path |
| `https://events.appsflyer.com/v1.0/c2s/session/app/` | Epic `FPXPluginCore.dll` | Same host, path stops at `/app/` |
| `https://events.appsflyer.com/v1.0/c2s/first_open/app/` | Epic `FPXPluginCore.dll` | Same |
| `https://events.appsflyer.com/v1.0/c2s/inapp/app/` | Epic `FPXPluginCore.dll` | Same |
| `https://ame.funplus.com/do?p0=pc&p1=8&p2=2580&p3=api` | Steam `FPX.dll` | FunPlus API string |
| `https://pc-client-callback.funplus.com:23451/callback` | Steam `FPX.dll` | Callback URL. Local resolver returned `127.0.0.1`; public DNS was not confirmed |
| `https://pc-client-callback.funplus.com:23451/discord/callback` | Steam `FPX.dll` | Discord callback URL in the same DLL |
| `https://partner.steam-api.com/ISteamMicroTxn/InitTxn/v3/` | Steam `FPX.dll` | Steam microtransaction API |
| `https://funplus.com/user-privacy-policy/` | Steam `FPX.dll` | Policy link |
| `https://funplus.com/user-agreement/` | Steam `FPX.dll` | Terms link |
| `https://discord.com/api/v10/` and related Discord OAuth and CDN URLs | Steam `FPX.dll` | Discord partner strings |
| `https://graph.facebook.com/`, plus WhatsApp, X, and TikTok share URLs | Steam `FPX.dll` | Share links |
| `pc.crashsight.wetest.net` | CrashSight log of both the Steam and Epic processes | Log text, not a URL template extracted from `CrashSight64.dll` |

`FPXEpic.dll` contains the word AppsFlyer and no `https://` URL in the strings that were extracted. `fpx_config_pc` on the Epic tree is a 20,504-byte file whose plain-text scan showed no `http` URL. Its contents were not decoded.

A development payment URL for `store-payment-cn-dev.nenglianghe.cn` is also embedded in the Steam `FPX.dll`. It was not seen as a live connection.

<h3 style="text-align:center">Observed TCP peers</h3>

RIPE Stat prefix overview, accessed 2026-10-11. No TLS server name was captured, so the peer is the network holder, not a confirmed hostname.

| When | Process | Remote | Port | State | Prefix holder |
| --- | --- | --- | --- | --- | --- |
| 17:17 and 17:20 | Steam client | `47.245.164.0`, `8.219.197.133`, `43.111.224.154` | `10000` | Established | Alibaba ASN `45102` |
| 17:17 | Steam client | `8.209.80.247` | `20000` | Established | Alibaba ASN `45102` |
| 17:20 | Steam client | `47.245.180.250` | `20000` | Established | Alibaba ASN `45102` |
| 17:17 | Steam client | `47.90.219.239` | `443` | Established, later CloseWait | Alibaba ASN `45102` |
| 17:20 | Steam client | `47.89.139.22` | `443` | Established | Alibaba ASN `45102` |
| 17:17 and 17:20 | Steam client | `43.153.246.111` | `443` | CloseWait | Tencent ASN `132203` |
| 17:34 | Epic client | `47.245.180.147` | `10000` | Established | Alibaba ASN `45102` |
| 17:34 | Epic client | `47.245.160.135` | `20000` | Established | Alibaba ASN `45102` |
| 17:34 | Epic client | `47.253.108.56` | `443` | Established | Alibaba ASN `45102` |
| 17:34 | Epic client | `43.153.246.111` | `443` | CloseWait | Tencent ASN `132203` (same address as the Steam session) |
| 17:34 | Epic client | `104.18.124.108` (three sockets) | `443` | Established | Cloudflare ASN `13335` |
| 17:34 | Epic client | `32.195.183.168` | `443` | Established | Amazon ASN `14618`. PTR `ec2-32-195-183-168.compute-1.amazonaws.com` |

Both processes also had a loopback TCP pair to themselves, and a UDP socket bound with no remote address. `events.appsflyer.com` resolved to `3.174.193.38`, `3.174.193.82`, `3.174.193.121`, and `3.174.193.122`. No TCP connection to those addresses was present after the Steam snapshots. The Epic snapshot was not compared against a fresh AppsFlyer resolution in the same minute; those four addresses were not among its peers.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">What those endpoints do not show</h2>

DNS-cache entries whose names contained `appsflyer`, `crashsight`, `funplus`, `aniimo`, `pawprint`, or `netease` were absent at 17:20:58, during the Steam session. Cache absence is a weak negative: short-lived lookups can expire, and many sockets were opened to raw IPs.

The prefix holder supports “the process talked to that network.” It does not label a socket as telemetry, authentication, or content delivery. Encrypted payloads were not read. The FunPlus callback host was not the loopback pair either game process actually held.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Vendor documents (not this session’s packets)</h2>

FunPlus International AG’s [User Privacy Policy](https://funplus.com/user-privacy-policy/) (last updated 23 December 2021, accessed 2026-10-11) says, in paraphrase, that player data can include an analytics ID, third-party account ids such as Steam, IP address, device and OS data, and usage data, and that analytics and storage providers may receive information. It also says FunPlus sometimes acts as a processor for a publisher. The policy is older than this PC client. It describes the company’s stated categories. It does not identify which of those fields the 2026-10-11 process sent.

`CrashSight64.dll` is signed by FunPlus International AG (Zug), the same legal name as that policy, product name CrashSight. The signature shows who signed the file. It does not show the contents of a crash report.

The Aniimo site `https://www.aniimo.com/` was fetched the same day. The converted page was marketing and launch-event text and did not include a privacy policy. A game-specific policy URL was not established here.

AppsFlyer’s own materials, already cited from [Telemetry and analytics](../../concepts/telemetry-and-analytics.md), describe identifier use for attribution. They remain general product documentation.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Identifier `759396`</h2>

Kept separate from `4126040` until a link is shown.

| Check | Result |
| --- | --- |
| `AF_counter_759396` during the Steam session and the later Epic session | Stayed `0`. `AF_counter_4126040` stayed `49` after the Epic launch. No new `AF_*` name appeared |
| Literal `759396` in `Aniimo.exe`, `GameAssembly.dll`, `FPX*.dll`, `NEP2.dll`, and the other targeted binaries | No match |
| Steam manifests in the two libraries checked | No `appmanifest_759396.acf` |
| Steam `appdetails?appids=759396` | `{"759396":{"success":false}}` |
| Public store URL fetch | Did not return an application page |
| `%LOCALAPPDATA%\appsflyer` file bytes versus the `759396` UUID | No match |

A third-party statistics site associates this number with a different commercial game. That page was not corroborated by Steam’s `appdetails` response or by the store URL fetch, so it is not adopted. Origin stays <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown**.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Unverified explanations</h2>

| Explanation | Label | Gap |
| --- | --- | --- |
| The FunPlus `FPX` / `FPXSteam` code is what creates `AF_uuid_<appid>` and `AF_counter_<appid>` | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> | Strings and URLs match the observed names. No API trace of the write. Another build of the same SDK could explain `759396` |
| This launch moved the `4126040` counter from 48 to 49 | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> | No read in the minute before 17:14:47. It did not keep rising between 17:15 and 17:22 |
| The December 2024 `%LOCALAPPDATA%\appsflyer` files belong to Aniimo | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> | They predate the September 2026 release and were not updated in this session |
| Port `443` to the Tencent prefix is CrashSight | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> | CrashSight’s resolved address was a different IP, and SNI was not captured |
| The protector driver exfiltrates data | **Not established** | No driver traffic was attributed |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Privacy assessment (current)</h2>

| Question | Current answer |
| --- | --- |
| Is an analytics SDK present in the files? | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> AppsFlyer endpoints and `AF_*` names in the FunPlus `FPX` binaries |
| Was an AppsFlyer upload observed while the game was running? | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Not in the TCP snapshots. A short connection could have been missed |
| Can network metadata alone name the personal data sent? | No. IPs, ports, and ASNs do not reveal an encrypted body |
| Is the behaviour malicious? | **Not established** |
| Did the disabled Startup Apps entries stop collection? | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> The values are still present. The game process ran anyway |

> **ℹ️ Note**
>
> A commercial attribution SDK and a kernel protector are privacy and trust facts. They are not, by themselves, a malware determination.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📚 Sources</h2>

- [Investigation log](investigation-log.md) (primary observational record)
- Steam `appdetails` for `4126040` and the negative result for `759396`, accessed 2026-10-11
- RIPE Stat prefix overview for the peer addresses, accessed 2026-10-11
- <a href="https://funplus.com/user-privacy-policy/" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> FunPlus User Privacy Policy</a>, accessed 2026-10-11
- AppsFlyer documents linked from [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Aniimo overview](README.md)
- [Technical overview](technical-overview.md)
- [Threat assessment](../../concepts/threat-assessment.md)
