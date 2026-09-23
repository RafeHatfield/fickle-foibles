# ficklefoibles.com

The Fickle Foibles site: a studio landing page and a page per game, plus the
**Support** and **Privacy Policy** pages Apple requires a URL for.

Static HTML and one stylesheet. No build step, no JavaScript, no web fonts, no
third-party requests. These pages are an App Store support URL and must load
anywhere, always. **No em or en dashes anywhere on the site**; use a plain hyphen. Push to `main` and GitHub Pages publishes it.

## The URLs App Store Connect needs

| Field | URL |
|---|---|
| Support URL | `https://ficklefoibles.com/dwindle/support/` |
| Privacy Policy URL | `https://ficklefoibles.com/dwindle/privacy/` |
| Marketing URL (optional) | `https://ficklefoibles.com/dwindle/` |

## Layout

```
index.html              studio landing
404.html
CNAME                   ficklefoibles.com  (do not delete: it is the custom domain)
.nojekyll               serve files as-is, no Jekyll
assets/style.css        every style on the site
assets/icon.svg         favicon
assets/dwindle/*.jpg    screenshots, downscaled from dwindle/store/screenshots/6.9/
dwindle/index.html      game page
dwindle/support/        Support URL
dwindle/privacy/        Privacy Policy URL
gemfall/index.html      in development
under-warden/index.html in development
```

## Where the words come from

The support and privacy text is the copy held in the Dwindle repo at
`store/support.md` and `store/privacy.md`, which is the source of truth. If the
game changes what it stores or asks for, change it there **and** here, and move
the "Last updated" date on the privacy page.

The palette in `assets/style.css` is lifted from Dwindle's own
`src/ui/theme/palette.gd` (both the light and the dark set) so the site and the
game are recognisably the same object.

## Adding a game

Copy `dwindle/` to `<game>/`, rewrite the three pages, add a card to the
`.game-list` in `index.html`, and drop screenshots in `assets/<game>/`. A game still in development only needs the
one page; the footer links on each page are hand-written, so update them too.

Gemfall's copy must always use the full title **Gemfall: The Dead City**, never bare
"Gemfall" as a heading or title (see `docs/POSITIONING.md` in the Gemfall repo).

## Regenerating the screenshots

From a checkout of the Dwindle repo, after `scripts/store_shots.sh`:

```sh
cd path/to/fickle-foibles
for f in 01_board 03_win 05_home 04_dark; do
  sips -Z 1120 "$DWINDLE/store/screenshots/6.9/$f.png" --out "/tmp/$f.png"
  sips -s format jpeg -s formatOptions 88 "/tmp/$f.png" --out "assets/dwindle/$f.jpg"
done
```

## Pointing the domain at GitHub

The domain is registered at Hover. Until its DNS points at GitHub, nothing is
reachable: `CNAME` makes `rafehatfield.github.io/fickle-foibles/` 301 to
`ficklefoibles.com`, so that address is not a fallback.

At hover.com → **ficklefoibles.com** → **DNS**, remove the parking records
(a single `@` A record and a `www` record, both `216.40.34.41`) and add:

| Type | Host | Value |
|---|---|---|
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |
| CNAME | www | rafehatfield.github.io. |

The four A records are GitHub's published apex addresses. AAAA records
(`2606:50c0:8000::153` through `...8003::153`) are optional and only add IPv6.

Then, once it resolves, tick **Enforce HTTPS** in the repository's
Settings → Pages. It stays greyed out until GitHub has issued the certificate,
which takes a few minutes after DNS is correct, and the App Store URLs should
be `https://`.

Check it:

```sh
dig +short ficklefoibles.com          # expect the four 185.199.10x.153
curl -sI https://ficklefoibles.com/dwindle/support/ | head -1   # expect 200
```

## Checking it locally

```sh
python3 -m http.server 8000
# then open http://localhost:8000/
```

Root-relative links (`/assets/style.css`) work under that server and on the
custom domain. They would break under a `user.github.io/repo/` project URL:
if the custom domain is ever dropped, the links need a prefix.
