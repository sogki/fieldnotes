<h1 style="text-align:center">Permissions and persistence</h1>

[<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Concepts](README.md) · [<img src="../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../README.md)

How privilege boundaries and common persistence locations should be documented in Fieldnotes.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Short definition</h2>

**Permissions** describe what a process or user can change. **Persistence** describes mechanisms that cause software to run again after reboot or logon.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Why it matters</h2>

Many legitimate applications use persistence. Documenting *where* something persists is not the same as declaring it malicious.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">How it works</h2>

<h3 style="text-align:center">Permissions (typical scopes)</h3>

| Scope | Typical meaning |
| --- | --- |
| Standard user | Changes apply mainly to that user’s profile and `HKCU` |
| Administrator / elevated | Can change machine-wide settings, many services, and `HKLM` |
| SYSTEM / service accounts | Background components with broader machine access |

Always state which scope an observation belongs to.

<h3 style="text-align:center">Common persistence locations (Windows, non-exhaustive)</h3>

- User and machine `Run` / `RunOnce` registry keys
- Startup folders
- Scheduled tasks
- Services and drivers
- WMI event subscriptions
- Shell extensions and helper objects
- Browser or game-platform helpers

Document only locations actually inspected. See [Startup items](../commands/windows/startup-items.md) and [Services](../commands/windows/services.md).

Kernel-mode drivers and always-on or on-demand anti-cheat services are a distinct privilege tier from user `Run` keys. For a documentation-led example, see [VALORANT & Riot Vanguard](../applications/valorant-vanguard/README.md)—without treating kernel presence as proof of malware.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Practical example</h2>

The [Aniimo case study](../applications/aniimo/README.md) concerns values under the **current-user** `Run` key—not a machine-wide service. That scope distinction matters for impact and next steps.

By contrast, the [VALORANT & Vanguard case study](../applications/valorant-vanguard/technical-overview.md) concerns a vendor-documented **kernel-mode driver** and related services—machine-wide privilege with a different trust model than HKCU autostart values.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Common misconceptions</h2>

| Belief | Reality |
| --- | --- |
| Any `Run` key entry is malware | False — autostart is widely used |
| User-scoped equals harmless | False — user-scoped malware and telemetry both exist |
| Disabling in Startup Apps deletes the value | Not always — verify with a registry read |

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Security implications</h2>

- Prefer identifying the **writer** and **signed path** over reacting to the mechanism name alone.
- Note whether an entry was enabled, disabled, or deleted after observation.
- Persistence ability alone is not evidence of malice.

<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Aniimo technical overview](../applications/aniimo/technical-overview.md)
- [Threat assessment](threat-assessment.md)
- [Registry commands](../commands/windows/registry.md)
