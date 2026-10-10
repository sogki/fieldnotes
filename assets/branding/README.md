<img src="fieldnotes-mark.svg" alt="Fieldnotes" width="40" height="40" style="display:block;margin:0 auto">
<img src="sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h1 style="text-align:center">Fieldnotes visual identity</h1>

[<img src="fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Brand assets for the Markdown handbook. Mood: **lo-fi sakura night** — deep charcoal, magenta, soft purple.

<img src="sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Assets</h2>

| File | Use |
| --- | --- |
| `fieldnotes-banner.png` | Root README hero only (original artwork) |
| `fieldnotes-mark.svg` | Folder + heart mark on hub pages |
| `icon-chat.svg` | Case title / category — chat & social apps |
| `icon-game.svg` | Case title / category — games |
| `icon-mouse.svg` | Case title / category — general PC software |
| `icon-web.svg` | Category — websites (for future profiles) |
| `sakura-rule.svg` | Section divider (instead of `---`) |
| `fieldnotes-arrow-back.svg` | Back navigation |
| `fieldnotes-arrow-forward.svg` | Forward / table nav accent |
| `evidence-*.svg` | Confidence diamonds (white→red heat scale) |
| `risk-*.svg` | Command-risk shields (same heat scale) |

<img src="sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Palette</h2>

| Role | Hex | Notes |
| --- | --- | --- |
| Night / page depth | `#0d0610` | Near-black with a purple cast |
| Charcoal panel | `#1a0a14` | Discs behind arrows, dark surfaces |
| Sakura pink | `#e05697` | Primary accent |
| Soft blush | `#ff7eb6` | Highlights |
| Twilight purple | `#9b4dca` | Secondary accent / gradients |
| Mist text | `#f5e6ef` | Light copy on dark surfaces |
| Stamen | `#ffe6a8` / `#f0a020` | Sakura centers |

Shields / badges: solid fills with `e05697`, `9b4dca`, or `ff7eb6`. Avoid near-black badge bodies on dark themes (they look “empty”).

<img src="sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Voice in Markdown</h2>

GitHub READMEs cannot load custom CSS. Match the theme with:

1. **Root only:** original banner (`fieldnotes-banner.png`)
2. **Hub + case pages:** mark / sakura via `style="display:block;margin:0 auto"` (Cursor often ignores `align=`)
3. **Nav links:** forward arrow **beside** the label (not a separate table column); use **Markdown** `[label](path.md)` for file targets
4. **Back-links:** `fieldnotes-arrow-back.svg` (14×14) in the link label
5. **Section breaks:** full-width `sakura-rule.svg` — **never** Markdown `---`
6. **Evidence / risk labels** use the shared SVG markers below — never circle emoji

<h3 style="text-align:center">File link pattern</h3>

Cursor’s preview often drops HTML `<a href="file.md">` inside tables. Prefer Markdown:

```markdown
[<img src="../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Label](path.md)
```

HTML `<a style="color:#c77dff">` is fine for in-page `#anchors`.

<h3 style="text-align:center">Back-link pattern</h3>

```markdown
[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)
```

<h3 style="text-align:center">Hub header pattern</h3>

```html
<img src="../assets/branding/fieldnotes-mark.svg" alt="Fieldnotes" width="40" height="40" style="display:block;margin:0 auto">
<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">
```

<h3 style="text-align:center">Application case title</h3>

Put the category icon **inline** with a **centered** H1 (not above it):

```html
<h1 style="text-align:center"><img src="../../assets/branding/icon-chat.svg" alt="" width="40" height="40" style="vertical-align:middle;margin-right:10px">Discord</h1>
```

Center major headings (`h1`–`h3`) and category labels (`Games` / `Applications` / `Websites`) with `style="text-align:center"`.

| Kind | Icon |
| --- | --- |
| Chat / social applications | `icon-chat.svg` |
| Games | `icon-game.svg` |
| General PC software | `icon-mouse.svg` |
| Websites | `icon-web.svg` |

Do **not** place the Fieldnotes folder mark above Contents or case titles — that mark is for hub pages only.

<h3 style="text-align:center">Section divider</h3>

```html
<img src="../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">
```

Adjust `../` depth so the path reaches `assets/branding/`. Leave a blank line after the rule before the next heading.

<img src="sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Do not</h2>

- Put the full banner on every page
- Use Markdown `---` horizontal rules
- Commit screenshots that contain secrets or account UI
- Invent alternate evidence colours or shapes (use `evidence-*.svg` / `risk-*.svg` only)
- Add heavy HTML/CSS that GitHub will strip

<img src="sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Evidence and risk markers</h2>

Custom markers replace circle emoji. **Diamonds** = confidence; **shields** = command risk. Colour climbs a white→red heat scale (cooler / whiter = lower concern; redder = higher).

| Marker | File | Role |
| --- | --- | --- |
| <img src="evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> | `evidence-confirmed.svg` | Confirmed |
| <img src="evidence-strong.svg" width="20" height="20" alt="Strongly supported"> | `evidence-strong.svg` | Strongly supported |
| <img src="evidence-plausible.svg" width="20" height="20" alt="Plausible"> | `evidence-plausible.svg` | Plausible |
| <img src="evidence-unknown.svg" width="20" height="20" alt="Unknown"> | `evidence-unknown.svg` | Unknown |
| <img src="evidence-disputed.svg" width="20" height="20" alt="Disputed"> | `evidence-disputed.svg` | Disputed |
| <img src="risk-readonly.svg" width="20" height="20" alt="Read-only"> | `risk-readonly.svg` | Read-only |
| <img src="risk-state.svg" width="20" height="20" alt="State-changing"> | `risk-state.svg` | State-changing |
| <img src="risk-destructive.svg" width="20" height="20" alt="Potentially destructive"> | `risk-destructive.svg` | Potentially destructive |

Inline pattern (adjust `../` depth):

```html
<img src="../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="">
```
