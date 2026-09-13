# Decisions

Append-only log of architectural decisions. Newest at the bottom.

---

## 2026-09-12 — Push over HTTPS with the gh credential helper, not SSH

**Decided:** `origin` is `https://github.com/CuriousCilantro/bonsitaula.com.git`, with
`gh auth setup-git` supplying credentials. The `git@github-hobby:` SSH remote was
abandoned for this repo.

**Reason:** The SSH key behind the `github-hobby` alias (`~/.ssh/id_rsa_github_bondai`)
authenticates as GitHub user `deep-visionlab`, which has no write access to
`CuriousCilantro/bonsitaula.com`. Fetch worked (public repo), push did not. Rather than
re-key the account or add a cross-account collaborator, the push now rides the
`CuriousCilantro` gh token.

**Tradeoff:** Pushing depends on a gh token in the macOS keyring and on `CuriousCilantro`
being gh's active account — two accounts are stored and `SuryaWaters` is pull-only. A
stale or switched account produces an auth failure that looks like a repo problem. The
alternative (granting `deep-visionlab` write access) was rejected as it would cross-link
two identities on a personal repo.

---

## 2026-09-12 — Merge remote history instead of rebasing onto it

**Decided:** Local work was committed first, then `origin/master` was merged in
(`de4fe4f`), producing a merge commit.

**Reason:** `origin` had six commits authored through the GitHub web UI that were already
public. Rebasing local work over them would have been fine, but merging preserved both
lines without rewriting anything published. The edits did not overlap — remote touched
`<head>` and the hero, local touched the Projects grid — so `index.html` auto-merged with
no conflicts.

**Tradeoff:** A merge commit in an otherwise linear personal-site history. Accepted: on a
repo edited from both a laptop and the GitHub web editor, merges will keep happening, and
rebasing published commits is the worse habit.

---

## 2026-09-12 — Keep the two-file blog format; no static site generator

**Decided:** Posts stay as `blog/posts/<slug>.md` plus a hand-edited entry in
`blog/manifest.json`, parsed client-side by `marked` at page load. No build step was
introduced even though the manifest is duplicated metadata.

**Reason:** GitHub Pages serves the repo as-is, so there is nothing to run on deploy and
nothing to install to preview. Following the existing convention kept "add a post" at two
file changes.

**Tradeoff:** The manifest is a manual single point of failure — a post with no entry is
invisible, and an entry whose `slug` does not match its filename 404s. Both failure modes
happened today. It also means no server-rendered HTML: posts are invisible to anything
that does not run JS, including link-preview crawlers.

---

## 2026-09-12 — Project card copy is taken from the live store listing, not written from memory

**Decided:** The Sanctum and FlowTube descriptions were sourced by fetching their Chrome
Web Store pages and paraphrasing the stated functionality.

**Reason:** Invented descriptions of a real shipped product read plausibly and are wrong
in ways nobody notices.

**Tradeoff:** The listing does not separate free from paid features prominently, so the
FlowTube card currently describes PRO-tier features alongside free ones. Accuracy against
the listing is not the same as accuracy about what a visitor gets for free — flagged as an
open issue rather than silently guessed.
