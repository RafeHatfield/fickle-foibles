# ficklefoibles.com

The Fickle Foibles site: a studio landing page and a page per game, plus the
**Support** and **Privacy Policy** pages Apple requires a URL for.

Static HTML and one stylesheet. No build step, no JavaScript, no web fonts, no
third-party requests — these pages are an App Store support URL and must load
anywhere, always. Push to `main` and GitHub Pages publishes it.

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
CNAME                   ficklefoibles.com  (do not delete — it is the custom domain)
.nojekyll               serve files as-is, no Jekyll
assets/style.css        every style on the site
assets/icon.svg         favicon
assets/dwindle/*.jpg    screenshots, downscaled from dwindle/store/screenshots/6.9/
dwindle/index.html      game page
dwindle/support/        Support URL
dwindle/privacy/        Privacy Policy URL
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
`.game-list` in `index.html`, and drop screenshots in `assets/<game>/`.

## Regenerating the screenshots

From a checkout of the Dwindle repo, after `scripts/store_shots.sh`:

```sh
cd path/to/fickle-foibles
for f in 01_board 03_win 05_home 04_dark; do
  sips -Z 1120 "$DWINDLE/store/screenshots/6.9/$f.png" --out "/tmp/$f.png"
  sips -s format jpeg -s formatOptions 88 "/tmp/$f.png" --out "assets/dwindle/$f.jpg"
done
```

## Checking it locally

```sh
python3 -m http.server 8000
# then open http://localhost:8000/
```

Root-relative links (`/assets/style.css`) work under that server and on the
custom domain. They would break under a `user.github.io/repo/` project URL —
if the custom domain is ever dropped, the links need a prefix.
