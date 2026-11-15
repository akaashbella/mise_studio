# PR #1 conflict-resolution candidate

This branch merges upstream main into Parthiv's original contribution, without
rebasing or replacing his commits. It is a handoff candidate for the existing PR,
not a competing PR or a production deployment.

- Parthiv head: `898f5196fba2538581daae3d7894e2f4d447abf0`
- Original feature commits: `2805927`, `898f519`
- Upstream main: `7dcee63fa13e6141090620fdff2eae921e65444a`
- Integration branch: `akaashbella/mise_studio:integration/parthiv-pr1-conflicts`
- Isolated worktree: `/private/tmp/mise-studio-parthiv-integration`

## Conflicts and resolutions

| Conflict | Resolution |
| --- | --- |
| Directory move/file-location conflicts: `.env.example`, `.gitignore`, three `docs/` files, `lib/recipe-api.mjs`, catalog/legacy/provider modules, `package-lock.json`, `public/recipe-import.js`, and six `test/` files | Keep Parthiv's additions at the canonical repository root, preserving module-relative paths. No nested application remains. |
| `mise_studio/package.json` modified/deleted | Relocate Parthiv's package manifest to root beside the unchanged lockfile. Preserve scripts, dependencies and Node >=24.19.0 requirement. |
| `README.md` runtime/deployment sections | Combine Node API instructions with upstream's static Pages setup, hash routing, and backup guidance. Explicitly distinguish static features from server-dependent imports/search. |
| `public/app.js` render route detection | Dispose search bindings while retaining upstream hash-based route parsing. |
| `public/app.js` recipe library/search | Retain suggestion dropdown, local matching, draft opening and timing review; adapt recipe card links to hash navigation. |
| `public/app.js` recipe editor | Preserve import controls, source attribution, unknown yields/times and editable drafts together with upstream's timeline return navigation and relative/hash links. |
| `public/style.css` shared trailing styles | Retain upstream timeline links and backup controls alongside Parthiv's dropdown/import styles and shared recipe grid. |
| `server.mjs` route handling | Keep API dispatch and same-origin CSP, then retain upstream legacy-page redirects without redeclaring pathname. Preserve allowlist, headers, timeouts, compression and conditional caching. |
| `test.mjs` environment and library assertions | Keep import mocks/timers and both branches' tests. Adapt added navigation expectations to upstream hash routes and search control. |
| Clean-merge regression: workspace marker/backup interaction | Preserve Parthiv's existing optional schemaVersion=3 marker through validation/export; count only workspace collections in restore confirmation. Version-1 backups without the marker remain supported. No additional mandatory schema introduced. |

## Verification

Using official Node v24.19.0, at the repository root:

- `npm ci`: success; lockfile and package manifest unchanged from Parthiv's head.
- `npm test`: success, including `node test.mjs`, `node infrastructure.test.mjs`,
  and 69 Node tests from `test/**/*.test.*` (0 failures/skips).
- `npm run build`: success; syntax checks server, API, app, import adapter,
  storage and timeline model.
- `node --test lib/recipes/catalog/catalog.test.mjs`: 10 passed, 0 failures/skips.
- Added regression coverage for root/private asset denial, hash recipe links,
  imported metadata backup round-trips, optional workspace marker, legacy backup
  restoration and confirmation counts.
- Upstream Pages workflow and `public/index.html` preserved byte-for-byte.
- No unresolved index entries, conflict markers, stale nested application,
  independent favicon or ingredient-aggregation implementation.

Browser smoke testing is incomplete: the browser access host was unavailable;
a native Safari attempt timed out. A local server did start successfully with
an in-memory catalog. Automated workflow tests use a simulated DOM, not a browser.

## Remaining deployment/review risks

- GitHub Pages deploys only `public/` and cannot run Node/SQLite or `/api/recipe-*`.
  Search/import need same-origin Node hosting, writable catalog storage and any
  desired server-side provider credentials. No backend hosting is provisioned here.
- Provider/API tests use fixtures. Live provider credentials, quotas, availability,
  real-site extraction and browser keyboard/visual interaction need validation.
- `npm audit --json` reports one high-severity vulnerable transitive dependency:
  locked `undici@7.29.1` (via Cheerio), with fixes reported at >=7.30.1. The
  existing manifest/lockfile are preserved; dependency remediation and deployment
  security review remain with the feature author/maintainer. No audit fix was run.
- Parthiv's schemaVersion=3 marker, optional import metadata, nullable imported
  durations/yields and before-v3 browser backup behavior are retained, with
  compatibility tested against upstream workspace backups.
- Historical design documents describe an older nested checkout/sibling service;
  runtime imports are self-contained under root `lib/`. Treat those documents as
  design history rather than deployment instructions.

## Updating the original PR

After reviewing this candidate, Parthiv can run these commands in **his** clone
(on a clean worktree, where `origin` points to his own fork):

```sh
git fetch origin recipe-suggestion-dropdown
git switch recipe-suggestion-dropdown
git merge --ff-only origin/recipe-suggestion-dropdown
git fetch https://github.com/akaashbella/mise_studio.git integration/parthiv-pr1-conflicts
git merge --ff-only FETCH_HEAD
# Review and rerun tests before publishing:
git push origin recipe-suggestion-dropdown
```

This is a normal fast-forward only if his current branch remains an ancestor of
the candidate. If either fast-forward fails, stop and inspect newer work; do not
reset, rebase or force-push. Updating his original head updates PR #1 while
preserving authorship/history. This integration task does not push to his fork,
upstream, or either independent feature branch, and does not open a PR.
