# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this is

The static website for Owl Hours Studio, live at <https://owlhours.space/>.

- Hosted on GitHub Pages from `github.com/owlhoursspace/owlhourspace.github.io`, branch `main`
- `CNAME` holds the custom domain. Deleting or editing it takes the domain down.
- `assets/img/owlhours-logo.png` serves as logo, favicon, apple-touch-icon and og:image on
  every page.

## Deployment

**Pushing to `main` publishes immediately.** There is no staging environment and no preview
branch. Treat every change to `main` as going live.

Most of the git history reads "Add files via upload", which is the GitHub web UI's automatic
message - the owner edits pages through the browser, not from this clone. **Run `git pull`
before editing locally**, or local work will overwrite edits made on the web.

## This is not a Jekyll site, despite appearances

- `_config.yml` exists but sets only `markdown: kramdown`
- `_layouts/default.html` exists and contains Liquid tags (`{{ page.title }}`, `{{ content }}`)
- **No page has YAML front matter.** All ten pages start with `<!DOCTYPE html>`, so Jekyll
  never applies the layout to any of them. Verified across every `.html` file in the repo.

`_layouts/default.html` is a leftover from an older, different design: plain black background,
system sans-serif, highlight.js pulled from a CDN. It shares nothing with the current design.

**Do not "fix" pages by making them use `_layouts/default.html`.** That would replace the
current design with the abandoned one. If a layout system is genuinely wanted, write a new
layout that reproduces the current design first, then migrate pages one at a time and check
each one in a browser.

## Two page templates, deliberately different

| Group | Pages | Nav markup |
| ----- | ----- | ---------- |
| **A - site pages** | `/`, `/about/`, `/contact/`, `/tools/`, `/worlds/` | `.logo`, `.logo-mark`, `.navlinks`, `.nav-toggle`; content sits in `.wrap` |
| **B - product and document pages** | `/tools/ui-blur-pro-urp/` plus its `changelog/`, `docs/`, `license/`, and `/worlds/room-to-bloom/` | `.nav-left`, `.nav-logo`, `.nav-breadcrumb`, `.nav-links`; no `.wrap` |

Group B's breadcrumb is a feature, not an inconsistency. **Do not merge the two templates.**

## Every page carries its own CSS, and the copies have drifted

Each page has a single inline `<style>` block of roughly 4,000 to 11,000 characters. They look
like copies of one design system. They are not.

Measured across all ten pages: the longest run of identical leading rules is **0 rules** for
group A and **1 rule** for group B. Of roughly 71,000 characters of CSS, **73 characters** can
be lifted into a shared stylesheet without changing cascade order.

What this means in practice:

- **Editing one page's CSS affects only that page.** There is no shared stylesheet.
- A change intended to be site-wide has to be repeated in all ten files.
- Extracting a shared stylesheet is **not** a mechanical refactor. The pages disagree on
  concrete values, so unifying them means picking winners, which changes how some pages look.
  Treat it as a design decision that needs the owner's sign-off, not as cleanup.

## Known drift - measured, not guessed

The colour palette is consistent everywhere:

    --bg #08111C   --surface #101B27   --ink #ECE9E0   --ink-soft #A7B0A1
    --ink-dim #6E7A69   --sage #8FAE93   --amber #E3A855   --plum #B993A0

These differ between pages:

| What | Difference |
| ---- | ---------- |
| `:root` variable count | `index.html` has 14 and is missing `--mono`, `--sans`, `--serif` (it hardcodes the font stacks instead). Most pages have 17; `docs/` and `license/` have 19. |
| `body` font-size | 16px on `/`, `/tools/`, `/worlds/`; 15px on every other page |
| `p` colour | `rgba(236,233,224,0.82)` on `/`; `--ink-soft` elsewhere |
| `.wrap` max-width | 1160px on `/`, `/tools/`, `/worlds/`; 760px on `/about/` and `/contact/` |

Each of these is load-bearing for how that page currently looks. **Do not correct one in
passing while doing unrelated work.** Fix them as a deliberate, reviewed change or leave them.

## Previewing locally

Ruby and Jekyll are not installed, and are not needed. Pages use root-relative paths
(`/assets/...`), so opening a file with `file://` breaks images and links. Serve the folder:

```bash
python -m http.server 8777 --bind 127.0.0.1
```

Then open <http://127.0.0.1:8777/>.

To verify a CSS change did not alter anything unintended, read `getComputedStyle` values for
`body`, `nav`, `footer`, `h1` and `p` before and after, rather than judging by eye.
