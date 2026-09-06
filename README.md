# salhfn.is

Static site for Sálfræðistofa Hafnarfjarðar. Plain HTML and CSS, no build step.

## Flat structure

Every file sits at the top level — no folders. This is deliberate: GitHub's
browser uploader does not reliably accept folders, so the site is laid out to
match what a drag-and-drop upload actually produces.

```
index.html                  Homepage
404.html                    Not-found page
<name>.html                 One page per psychologist (10)
style.css                   All styling
<name>.jpg                  Portraits
hero.jpg                    Homepage hero
favicon-*.png               Icons
vercel.json                 Clean URLs, redirects, cache headers
```

Every internal link is root-relative (`/style.css`, `/anna-kristin-newton`),
so it resolves the same from any page.

## Local preview

Use a server — opening the file directly will load it unstyled, because `/`
means your hard drive's root over `file://`.

```
python3 -m http.server 8000
```

## Deploy

Push to GitHub, import the repo in Vercel, framework preset **Other**, leave
build command and output directory empty.

`cleanUrls: true` serves `anna-kristin-newton.html` at `/anna-kristin-newton`,
and the redirects map every old Wix URL to its new one as a 301.

## Open items

- [ ] **Ásrún vs Ástrún** — the old site says *Ásrún* on the homepage but
      *Ástrún* in her page title and URL. This build uses *Ásrún* throughout.
- [ ] **Þorbjörg vs Þórbjörg** — same conflict. This build uses *Þorbjörg*.
- [ ] **Email addresses** — seven of the ten publish personal Gmail addresses.
      Worth proposing `@salhfn.is` forwarding addresses, since addresses in
      `mailto:` markup get harvested by spam bots.
- [ ] Point the domain at Vercel only once the preview URL looks right, and
      keep Wix running until then.
