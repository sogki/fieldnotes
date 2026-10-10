<h1 style="text-align:center">🕵️ Discord — investigation log</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Hands-on research log (2026-10-10 UTC). Usernames/paths sanitised. Tokens, cookies, session material, keys, and GUIDs were not read or published.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Summary</h2>

| Field | Value |
| --- | --- |
| **Status** | Hands-on case study — local Stable inventory + official API docs |
| **Stable** | `1.0.9261` at `%LOCALAPPDATA%\Discord\app-1.0.9261` |
| **Processes** | 6× `Discord.exe` (parent launched via `Update.exe`) |
| **Startup** | HKCU Run → `Update.exe --processStart Discord.exe` |
| **Also discovered** | Discord Canary `1.0.1099` (ARP/Uninstall entry only; not asserted as running) |
| **API check** | `GET /api/v10/gateway` → `{"url":"wss://gateway.discord.gg"}` |

<h3 style="text-align:center">Not performed</h3>

- Reading Local Storage / Session Storage / IndexedDB  
- Parsing Sentry `scope_v3.json`  
- Gateway Identify or authenticated REST  
- Launching/quitting Discord for tests  
- Extracting or publishing credentials, session tokens, keys, or GUIDs  

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Chronology</h2>

<h3 style="text-align:center">1. ARP + path confirmation</h3>

Stable `1.0.9261` confirmed. An Uninstall/ARP entry for Discord Canary `1.0.1099` was also discovered (out of primary scope; not treated as a running-process observation). <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">

<h3 style="text-align:center">2. Process tree</h3>

One parent Stable `Discord.exe` with five children; updater parent had exited — matched SquirrelSetup.log updater launch. <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">

<h3 style="text-align:center">3. Signatures + modules</h3>

`Update.exe` / `Discord.exe` / several DLLs Authenticode Valid (Discord Inc. or Microsoft). Modules include voice, RPC, Krisp, overlay, hook. <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> / <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported">

<h3 style="text-align:center">4. Roaming profile</h3>

Chromium user-data layout; `sentry\` and `Crashpad\` present; `settings.json` keys reviewed without token fields. <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">

<h3 style="text-align:center">5. Official API refresh</h3>

Reconfirmed Get Gateway unauthenticated JSON URL. Documented REST/Gateway/OAuth2 remain as prior research. <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Commands executed</h2>

```powershell
Get-ItemProperty …\Uninstall\* | Where-Object { $_.DisplayName -match 'Discord' }

Get-CimInstance Win32_Process |
  Where-Object { $_.Name -match '^(Discord|Update)\.exe$' -and $_.ExecutablePath -match 'Discord\\' }

Get-ChildItem "$env:LOCALAPPDATA\Discord"
Get-ChildItem "$env:LOCALAPPDATA\Discord\app-1.0.9261\modules" -Directory
Get-AuthenticodeSignature <Update.exe, Discord.exe, selected DLLs>

Get-ChildItem "$env:APPDATA\discord"   # names only for sensitive stores
# settings.json: enumerate keys / non-secret scalars only

Get-Content "$env:LOCALAPPDATA\Discord\SquirrelSetup.log" -Tail 15
curl.exe -sS "https://discord.com/api/v10/gateway" -H "User-Agent: FieldnotesResearch/1.0 (documentation; read-only)"
```

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Live endpoint checks</h2>

| Request | Result | Limits |
| --- | --- | --- |
| `GET https://discord.com/api/v10/gateway` | 200; `{"url":"wss://gateway.discord.gg"}` | No WebSocket session |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Interpretation</h2>

1. Local Stable matches the documented Electron/Squirrel desktop model.  
2. Multi-process runtime and updater launch chain are directly observed for Stable.  
3. Crash/Sentry client artefacts exist locally; not treated as malice.  
4. Official developer APIs remain distinct from the logged-in desktop session.  
5. Canary appears only as a discovered install entry in this case study.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Follow-up</h2>

1. Optional: capture `--type=` command lines for child processes (still read-only).  
2. User-driven review of in-app Data & Privacy toggles.  
3. Do not export Local Storage for Fieldnotes.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Non-claims</h2>

- Discord is malware / proven safe  
- Sentry folder implies covert spying  
- All client HTTP routes are in the public developer docs  
- Canary was running during the snapshot  

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Case overview](README.md)
- [API and endpoints](api-and-endpoints.md)
