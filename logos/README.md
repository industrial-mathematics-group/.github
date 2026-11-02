# Industrial Mathematics Group: logo kit

*Open mathematics for the industry of tomorrow.*

The mark is a gear made of particles. Three groups of particles form applications: two **traffic lanes** running into a jam (red), a **wave** (blue) and a small **network** (green). Seen from far away, the individual particles merge into continuous colour bands, the step from particle to continuum descriptions.

## Contents

```
svg/
  avatar.svg / avatar-dark.svg                 rounded square tile (GitHub, Zenodo)
  avatar-circle.svg / avatar-circle-dark.svg   round tile
  mark.svg / mark-dark.svg                     gear only, transparent background
  logo.svg / logo-dark.svg                     tile + name + tagline (headers, slides)
png/
  avatar(-dark)-500.png, -1024.png
  avatar-circle(-dark)-1024.png
  mark(-dark)-512.png
  logo(-dark)-1200.png, -2400.png
favicon/
  favicon(-dark)-32.png, -180.png, -512.png
```

- **-dark** = for dark backgrounds.
- All text (Space Grotesk) is converted to outlines, so no fonts are needed.
- Neutral particles use a particle tone of 0.6, halfway between the gear fill and the outline colour.

## Where to use what

| Place | File |
|---|---|
| GitHub organisation picture | `png/avatar-dark-500.png` or `png/avatar-500.png` (GitHub accepts PNG/JPG and shows it with rounded corners) |
| Zenodo community logo | `png/avatar-1024.png` or `png/avatar-dark-1024.png` |
| Org profile README, website header | `svg/logo.svg` + `svg/logo-dark.svg` (snippet below) |
| Slides, posters | `svg/logo*.svg` (scales freely) or `png/logo*-2400.png` |
| Browser tab icon | `favicon/favicon-32.png`, Apple touch icon `favicon/favicon-180.png` |

## README header with automatic light/dark (GitHub and Forgejo)

For a GitHub organisation, the profile README lives in `.github/profile/README.md` of a repository named `.github`.

```html
<div align="center">

<img alt="Industrial Mathematics Group" src="logos/svg/logo.svg#gh-light-mode-only" width="640">
<img alt="Industrial Mathematics Group" src="logos/svg/logo-dark.svg#gh-dark-mode-only" width="640">

</div>
```

## Colours

| Role | Light | Dark |
|---|---|---|
| Outline / text | `#1B2238` | `#E6EAF4` |
| Gear fill | `#F2F5FB` | `#1D2233` |
| Background / hub | `#FFFFFF` | `#12151F` |
| Muted text | `#5A6378` | `#9AA3B8` |
| Traffic (Julia red) | `#CB3C33` | `#CB3C33` |
| Wave (Julia blue) | `#4063D8` | `#4063D8` |
| Network (Julia green) | `#389826` | `#389826` |

Typeface: Space Grotesk (SIL Open Font License).
