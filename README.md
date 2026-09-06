# salhfn.is — static rebuild

Static rebuild of Sálfræðistofa Hafnarfjarðar's site, migrating off Wix to
GitHub + Vercel. No framework, no build step — plain HTML, CSS and images.

## Structure

```
index.html                          Homepage
404.html                            Not-found page
vercel.json                         Clean URLs, redirects, cache headers
assets/css/style.css                All styling, both pages
assets/img/                         Hero, portraits, favicons
salfraedingar/<name>.html           One page per psychologist (10)
```

## Local preview

**You must use a server** — paths are root-relative (`/assets/...`), so
double-clicking `index.html` will load it without styles.

```
python3 -m http.server 8000
```

Then open `http://localhost:8000`.

## How the pages connect

Plain `<a href>`; the folder structure is the URL structure. Every internal
path starts with `/` so it resolves identically from any depth — copy a block
of markup between the homepage and a sub-page and nothing breaks.

`cleanUrls: true` in `vercel.json` serves `salfraedingar/linzi-trosh.html` at
`/salfraedingar/linzi-trosh`, so links are written without the `.html`.

There are no includes in static HTML, so the header and footer are duplicated
across all 12 files. That's fine at this size; if the site grows past ~15 pages
or the client starts asking for nav changes, move to Astro — it keeps the
output static but gives you one shared layout.

## Deploy to Vercel

1. Push this folder to a GitHub repo.
2. In Vercel: **Add New → Project → Import** the repo.
3. Framework preset: **Other**. Leave build command and output directory empty —
   Vercel serves the repo root as static files.
4. Add the domain `salhfn.is` (and `www.salhfn.is`) under **Settings → Domains**,
   then point the nameservers or A/CNAME records away from Wix.

## Fidelity notes

Measured against the live Wix site at a 1905px content width. The rebuild
reproduces its geometry exactly:

| Metric              | Original | Rebuild |
|---------------------|----------|---------|
| Page height         | 5607px   | 5378px* |
| Hero height         | 837px    | 837px   |
| Grid container      | 904px    | 904px   |
| Column pitch        | 517px    | 517px   |
| Row pitch           | 815px    | natural* |
| Portrait            | 350px ⌀  | 350px ⌀ |
| Section title       | 56px     | 56px    |
| Name / body         | 35 / 20px| 35 / 20px |

\* The footer was deliberately redesigned and the card rows now size to their
content, so total page height no longer matches. Column positions, portrait
size and type scale are unchanged.

Cards are laid out with `grid-auto-flow: column` over 5 rows, so DOM order is
column-major: the first five articles form the left column, the last five the
right. Each card is a flex column with `margin-top: auto` on the button, so
within any row the two cards' portraits, names and buttons align exactly.

## Deliberate differences from the Wix original

- **`lang="is"`** instead of `lang="en"`. The original declares English on an
  Icelandic site, which hurts SEO and screen readers.
- **Clean URLs.** Wix slugs are `/1`, `/2`, `/copy-of-trausti-valsson`,
  `/copy-of-anna-kristín-newton`. Sub-pages here use
  `salfraedingar/<name>.html`. Set up redirects from the old slugs at launch so
  existing links and search rankings survive.
- **Images optimised**: 9.2 MB → 1.03 MB. Ásta's portrait was a 7 MB
  3812×5718 original; portraits are now 700px squares (2× the 350px display
  size), hero is 2400px wide.
- **Wordmark is live text**, set in Cormorant Garamond, rather than the
  `logo-hvitt.png` bitmap the original upscales from 456×122 to 580×157.
  Crisper on retina and indexable. To use the real file instead, drop it in
  `assets/img/` and swap the `.wordmark` paragraph for an `<img>`.
- Added `description`, Open Graph tags, and `MedicalBusiness` structured data.
- **"Lesa nánar" is a button**, not a plain underlined text link. Outlined at
  rest; on hover and keyboard focus it fills with `--ink`, lifts 1px and nudges
  its arrow 4px right. All transitions are disabled under
  `prefers-reduced-motion`.
- **Footer redesigned.** The original's "Staðsetning" heading is gone. The
  footer is now a distinct band — light ground (`--footer-bg`), hairline top
  rule, clinic name and address centred — rather than centred text floating on
  white with ~430px of padding around it.

## Open items

- [ ] **Consider clinic-domain email addresses.** Seven of the ten psychologists
      publish personal Gmail addresses; only Erla and Pétur use their own
      domains. Addresses in `mailto:` markup get harvested by spam bots, so
      `@salhfn.is` forwarding addresses would be worth proposing as part of the
      move.
- [ ] **Confirm the Ásrún portrait.** Verified against the live site: her own
      profile page uses `0.jpg` — the same file as Anna Kristín. So the
      duplicate isn't a homepage slip, it's site-wide, and one of them has the
      wrong photo published. `0 (2).jpg` from the Drive folder is an unused
      portrait of a different woman and is assigned to Ásrún here. Confirm with
      the clinic before launch.
- [ ] **Two name spellings disagree on the live site.** Homepage says *Ásrún Á.
      Jónsdóttir*; her page title and URL both say *Ástrún*. Homepage says
      *Þorbjörg Sveinsdóttir*; her page title says *Þórbjörg*. This build uses
      the homepage spellings throughout so there's a single source of truth —
      change both with find-and-replace once the clinic confirms.
- [ ] Drop in the real `logo-hvitt.png` if the bitmap wordmark is preferred.
- [ ] Build the 10 sub-pages.
- [ ] Consider legibility of the hero wordmark and intro text — both sit over
      the brightest part of the sky and are close to invisible on the original.
