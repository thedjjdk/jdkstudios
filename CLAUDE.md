# JDK Studios site

Static portfolio site for JDK Studios. Plain HTML/CSS/JS — no build step, no
dependencies. Served by GitHub Pages at https://thedjjdk.github.io/jdkstudios/

## Files

- `index.html` — the site. Everything lives here: styles, markup, scripts.
- `desktop-view.html` — an alternate "desktop OS" view with a file-explorer UI.
  It mirrors the same content as `index.html` via JS arrays.
- `images/` — all media.
- `.nojekyll` — bypasses Jekyll processing on branch-based Pages deploys.

## Adding a band, venue, or credit

Content changes almost always touch **both** HTML files. Editing only
`index.html` leaves the desktop view out of sync.

1. Add the image to `images/`.
2. In `index.html`, copy an adjacent `media-card` block and edit it in place.
   Order within the grid is source order — insert where it should appear.
   - Square photo: `object-fit:cover` (fills the tile, like the band photos).
   - Wide logo/banner: `object-fit:contain` plus a `background:` on
     `.media-card-inner` matching the logo's own background, so the tile reads
     as one piece. See the Clockwork Entertainment card for an example.
3. In `desktop-view.html`, add the file in the **same position** to both:
   - `fileLabels` — maps filename to display name.
   - the relevant section array (`liveSoundFiles`, `studioFiles`, etc.).

## Publishing

Work on a branch, then squash-merge into `main`. Pushing to `main` is what
triggers deployment.

```
git fetch origin main && git checkout -B <branch> origin/main
# edit, commit
git push -u origin <branch>
# open PR, squash merge into main
```

### Always verify the deploy actually ran

A successful merge does **not** mean the site updated. Check that a run appeared
for the merge commit before telling anyone it is live:

```
mcp__github__actions_list method=list_workflow_runs owner=thedjjdk repo=jdkstudios
```

Two runs should appear per push to `main`, both currently active:
`pages build and deployment` (branch-based) and `Deploy static content to Pages`
(`.github/workflows/static.yml`). Either one publishes the site.

The response is large — parse it rather than reading it whole:

```
python3 -c "
import json; d=json.load(open('<saved-file>'))
[print(r['created_at'], r['name'], r['status'], r.get('conclusion'), r['head_sha'][:7]) for r in d['workflow_runs'][:5]]"
```

### If no run appears for your merge

Merged commits are **not** deployed retroactively — if Actions was disabled or
stuck when the merge landed, that commit will never deploy on its own. It needs
a *new* push to `main` to trigger a build. An empty commit is not always enough;
a real file change is more reliable.

This happened on 2026-08-06: four merges landed with zero deploy runs while the
repo's Actions/Pages settings were misconfigured, leaving the live site frozen
on the previous deploy for a full day. Fixing the settings did not republish the
backlog — a fresh push was required.

Note: triggering or re-running a workflow via the API returns
`403 Resource not accessible by integration`. Workflows cannot be started
directly; deploys must come from a push, or from JDK clicking "Run workflow" in
the Actions tab.

### Cache

After a deploy, the browser will often still show the old page. Hard refresh, or
use a private tab. The Instagram in-app browser caches especially aggressively —
check in Brave or Safari directly before concluding a change did not publish.
