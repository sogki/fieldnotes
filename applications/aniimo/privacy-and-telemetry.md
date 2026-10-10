<h1 style="text-align:center">Aniimo — privacy and telemetry</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Aniimo](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Separates **user observations**, **third-party claims**, and **unverified explanations**. No claim that Aniimo is malicious or free of data collection.

Authoritative chronology: [investigation log](investigation-log.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">🔎 Observations (this installation)</h2>

| Observation | Confidence |
| --- | --- |
| Four `AF_uuid_*` / `AF_counter_*` values under the current-user `Run` key | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| UUID-style data redacted as `[REDACTED GUID]`; counters `48` and `0` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Entries appeared disabled in Startup Apps / Task Manager without ordinary executable paths | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> UI observation |
| Suffix `4126040` matches Aniimo’s Steam app ID; user installed/played Aniimo; Steam metadata included `4126040` | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> |
| Suffix `759396` unidentified; not found in checked Steam manifests / reported installed list | Observation <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed">; meaning <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |
| Defender Quick scan reported no threats | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Scan result |
| Binary analysis, packet capture, network inspection | **Not performed** |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Third-party claims (not verified here)</h2>

| Claim | Treatment |
| --- | --- |
| Community reports associate `AF_uuid_[APPID]` / `AF_counter_[APPID]` with AppsFlyer analytics | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> **Plausible** lead only |

> **❓ Unverified**
>
> Official AppsFlyer materials describe attribution SDKs that may use device and install identifiers. That general product behaviour does **not** prove AppsFlyer wrote these registry values.

See [Telemetry and analytics](../../concepts/telemetry-and-analytics.md).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Unverified explanations</h2>

| Explanation | Label | Gap |
| --- | --- | --- |
| Aniimo or a shipped component wrote the `4126040` pair | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> | No writer process or install-time trace |
| `AF_*` naming indicates AppsFlyer (or similar) | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> | Naming + community reports only |
| GUID values are stable attribution / install IDs | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> | Common pattern; not proven here |
| Counters track launches or events | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> | Semantics unverified |
| `759396` is another Steam app or bundled product | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> | No positive identification |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Privacy assessment (current)</h2>

| Question | Current answer |
| --- | --- |
| Is telemetry present? | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Naming suggests analytics patterns; collection not demonstrated |
| Is behaviour malicious? | **Not established** |
| Are identifiers leaving the device? | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> No network observation |
| Did disabling Startup Apps stop any collection? | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> |

> **ℹ️ Note**
>
> Unfamiliar registry entries and analytics-related identifiers do not, on their own, demonstrate malicious activity.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Evidence that would raise confidence</h2>

Listed as not-yet-performed follow-up in the [investigation log](investigation-log.md#follow-up-research-not-yet-performed).

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">📚 Sources</h2>

- [Investigation log](investigation-log.md) (primary observational record)
- Community AppsFlyer naming association — unverified for this case
- Official analytics / privacy references under [Telemetry and analytics](../../concepts/telemetry-and-analytics.md)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Aniimo overview](README.md)
- [Technical overview](technical-overview.md)
- [Threat assessment](../../concepts/threat-assessment.md)
