# Project State

Personal site: static HTML, no build step, deployed by GitHub Pages from
`CuriousCilantro/bonsitaula.com` (`master`) to https://bonsitaula.com (CNAME in repo root).

Last updated: 2026-09-12

## What's working

Verified today by HTTP request, not by eye — see the caveat at the end of this section.

- **Live deploy.** `master` is in sync with `origin/master` at `de4fe4f`. Live 200s
  confirmed for `/`, `/blog/manifest.json`, `/blog/posts/On%20AI.md`.
- **Blog pipeline.** Adding a post takes exactly two file changes and nothing else:
  1. `blog/posts/<slug>.md` — body only, no frontmatter, no `# H1` title
  2. `blog/manifest.json` — one entry: `slug`, `title`, `date`, `readTime`, `tags`
  `slug` must equal the `.md` filename; that string is the only link between the two.
  `index.html` (top 3), `blog/index.html` (all), and `blog/post.html?slug=…` all `fetch()`
  the manifest at runtime. Markdown is parsed client-side by `marked@14` from jsDelivr.
- **One post published:** "On AI" (slug `On AI`). Live manifest contains it and the
  `.md` serves over the encoded path.
- **Projects section:** Dolpo, Sanctum, FlowTube. Kickstarter Analysis removed —
  0 occurrences in the live HTML.
- **Music Projects section removed.** The whole `<section id="music">` block (the
  "Finding MIA" coming-soon card) is gone from `index.html`. Nothing linked to `#music`,
  so no dangling anchors. Removed locally after the last push — not yet live.
- **Push path.** `git push origin master` works over HTTPS with the gh credential
  helper as GitHub user `CuriousCilantro`. Pushed `a25cd65..de4fe4f` today.
- **Local preview.** `python3 -m http.server 8000` from the repo root; `index.html`,
  `blog/index.html`, `blog/manifest.json`, `blog/posts/On%20AI.md`, `quotes/index.html`
  all returned 200.

Caveat, stated plainly: every check above was `curl` status codes plus grep of the
returned HTML source. **No page was opened in a browser this session**, so the
JS paths — manifest `fetch()`, `marked.parse()`, the theme toggle — are unverified
end-to-end today. They are the same code paths that were working before today and
none of them were edited, but that is inference, not observation.

## In progress

Nothing half-built. Two loose ends:

- `blog/post.html` carries an **uncommitted, whitespace-only prettier reformat** that
  predates this session. Not authored here, deliberately left out of today's commits.
  Either commit it alone or `git checkout -- blog/post.html` to drop it.
- The FlowTube card description is written but its accuracy is unresolved — see
  Known issues.

## Known issues

- **FlowTube description overstates the free tier.** The card says it hides
  "comments, end-screen cards, sidebar suggestions, and the homepage feed"
  (`index.html`, FlowTube card). Per the live Chrome Web Store listing, only comments
  and end-screen cards are free; sidebar, "Up Next", and homepage feed are the $4.99
  PRO tier. Decide: describe the free tier only, or label it as the full product.
- **Post slug contains a space and capitals:** `"On AI"` → `blog/posts/On AI.md`.
  It works on GitHub Pages (verified via `%20`), but it is the one string that must
  match a filename byte-for-byte, and any host that normalizes paths will break it.
  Renaming to `on-ai` means changing the filename and the manifest `slug` together.
- **`<meta name="description" content="">` is empty** in `index.html` (came in from a
  remote commit, not from this session). Kills search snippets and link previews.
- **Contact email is plain text in the hero** (`index.html`, hero-bio) — scrapeable.
  Deliberate as far as this session knows; noting it, not flagging it as a mistake.

## Next session should start with

Resolve the FlowTube card copy, since it is the only thing currently on the live site
that may be factually wrong: open the store listing, decide whether the card should
describe the free tier or the full paid product, and edit the one `project-desc` block
in `index.html`. Then, if you want the blog URLs made durable, rename
`blog/posts/On AI.md` to `blog/posts/on-ai.md` and change the manifest `slug` to `on-ai`
in the same commit — the two must move together or the post 404s. Both are small; the
whole thing is about 10 minutes including a local server check.

## Do not touch / watch out for

- **Do not switch `origin` back to SSH.** The `github-hobby` SSH alias uses
  `~/.ssh/id_rsa_github_bondai`, which GitHub resolves to user **deep-visionlab** —
  no write access to this repo. That is what caused today's `Permission denied`.
  `origin` is now HTTPS on purpose.
- **gh has two accounts stored.** `CuriousCilantro` (admin/push) must be the active
  one; `SuryaWaters` has pull only (`"push": false`). Check with `gh auth status`
  before blaming git for a rejected push.
- **Never open the site with `file://`.** `fetch()` is blocked there, the manifest
  load fails silently, and the page shows "Could not load posts." Always go through
  a local HTTP server. This looked like a broken blog twice today.
- **A committed `.md` with no manifest entry shows nothing, and vice versa.** Today the
  live manifest was `[]` while the post file existed and was unpushed — the blog looked
  empty for reasons that had nothing to do with the page code. Check
  `git show origin/master:blog/manifest.json` before debugging the frontend.
- **`blog/manifest.json` is hand-formatted** — `tags` on one inline line. Running it
  through `json.dump`/`json.tool` reformats the whole file and buries the real change.
  Edit it by hand.
- **Do not repeat the post title as an `# H1`** in the `.md`. `blog/post.html` renders
  the title from the manifest; an H1 duplicates it on the page.
- **Untracked files with near-identical content block a merge.** `blog/posts/On AI.md`
  existed untracked locally while `origin` had committed its own copy differing only by
  a trailing newline. Normalizing to the committed bytes first is what kept the merge
  clean.
