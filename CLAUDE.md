# CLAUDE.md

Instructions for regenerating / maintaining this profile README.

## What this repo is

GitHub profile README at `andrasbacsai/andrasbacsai`. Single file: `README.md`. Renders on `https://github.com/andrasbacsai`.

## Style direction

**Bold personal brand.** Typographic banner, custom 2-col project cards (no plain pin cards), tokyonight palette, focused socials.

Brand color: `#9333ea` (purple). Card label background: `#1a1b27`.

## Structure (top-to-bottom)

1. **Banner** — `capsule-render` SVG, name + tagline
2. **Action chips** — Coolify, Sponsor, X, Discord (shields.io `for-the-badge`)
3. **whoami** — 1 paragraph, founder pitch
4. **whatamidoing** — 6 hero projects in 3×2 HTML table
5. **whatelse** — 2 secondary projects in 1×2 HTML table (same card style)
6. **stats for neds** — org-stars badge + 5 `github-profile-summary-cards`
7. **Footer** — building-in-the-open line + inline links

## Card template

Each project cell uses this exact pattern:

```md
### [Name](https://github.com/coollabsio/<repo>)
One-line pitch. Past period.

![stars](https://img.shields.io/github/stars/coollabsio/<repo>?style=flat&color=9333ea&labelColor=1a1b27) ![lang](https://img.shields.io/badge/<Lang>-<hex>?style=flat&labelColor=1a1b27)
```

Wrap pairs of cells in `<table><tr><td width="50%" valign="top">…</td><td width="50%" valign="top">…</td></tr></table>`.

## Updating content

### Add / swap a hero project
Replace one `<td>` block in the `whatamidoing` table. Keep 2-col 3-row grid (6 total). Pick lang badge color from official brand color.

### Move project from heroes → whatelse (or vice versa)
Cut the `<td>` block, paste into the other table. Card markup is identical.

### Refresh org star total

```bash
gh api orgs/coollabsio/repos --paginate -q '[.[] | select(.archived==false and .fork==false) | .stargazers_count] | add'
```

Round down to nearest 1k. Update the badge:

```md
[![coollabsio stars](https://img.shields.io/badge/⭐_stars_across_@coollabsio-<N>k+-9333ea?style=for-the-badge&labelColor=0d1117)](https://github.com/coollabsio)
```

### Change tagline / banner desc
Edit URL fragment after `desc=` in the `capsule-render` `<img>` (line near top of README). URL-encode spaces as `%20`, dots/dashes left as-is. Verify URL returns 200 before committing.

### Change theme
Search-replace `tokyonight` → other supported theme name (e.g. `radical`, `gruvbox`). Applies to both `github-readme-stats` pin URLs and `github-profile-summary-cards` URLs. Verify same theme name works on both services (they don't always agree — `github-profile-summary-cards` rejects `tokyo_night`, accepts `tokyonight`).

## External services used

| Service | Purpose | Notes |
|---|---|---|
| `capsule-render.vercel.app` | Header banner SVG | Params: type, color, customColorList, height, text, fontSize, desc, animation |
| `img.shields.io` | All badges | `for-the-badge` for chips, `flat` for card metadata |
| `github-readme-stats.vercel.app` | (currently unused — pin cards removed) | Cache 4h |
| `github-profile-summary-cards.vercel.app` | 5 stats cards | Theme `tokyonight` (no underscore) |

If any service goes dark, swap to alternate. Don't replace with rasterized screenshots.

## Verification checklist before committing

```bash
# All image URLs return 200
grep -oE 'https?://[^")]+' README.md | sort -u | while read u; do
  printf '%s -> %s\n' "$u" "$(curl -sIL -o /dev/null -w %{http_code} --max-time 10 "$u")"
done
```

Then open `https://github.com/andrasbacsai` in browser. Toggle GitHub light/dark theme. Confirm:
- Banner renders, no broken-image icon
- All 8 project card titles are clickable links
- Stats cards load (5 cards)
- Mobile viewport: tables stack OK

## Don't do

- Don't add emoji to section headers (kept lowercase, dryly named: `whoami`, `whatamidoing`, `whatelse`, `stats for neds`).
- Don't reintroduce the `<!-- … -->` GitHub template boilerplate that was in the original README.
- Don't switch to plain pin cards for project showcases — custom 2-col table is the established style.
- Don't hardcode raw star counts inside card descriptions; use the live `shields.io/github/stars/...` badge.
