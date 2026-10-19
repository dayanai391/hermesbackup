# Automated Backups to GitHub (scheduled git sync)

## When to Use

Use this when the user asks to back up or sync local files to a GitHub repo
on a schedule ("hourly backup into my repo", "keep X pushed to GitHub"), or
needs any recurring unattended git push driven by a `cronjob` script. For
one-off pushes, PRs, or auth problems, route to `references/pr-workflow.md`
/ `references/auth.md` instead.

Pattern for pushing a snapshot of local files (app state, notes, configs) to
a user-owned GitHub repo on a schedule. Distilled from a working hourly
backup of `$HERMES_HOME` state; adapt the file list per target.

## Setup order

1. **Resolve the repo before writing anything.** Users give partial names
   ("the hermesbackup repo"). List the token owner's repos and match:
   `curl -s -H "Authorization: token $T" https://api.github.com/user` for
   the login, then `/user/repos` (or `/repos/<owner>/<name>` directly).
   A bare `/repos/<name>` 404s — repo names are not global on GitHub.
   If the repo doesn't exist, create it (`gh repo create` or POST
   `/user/repos`) rather than assuming the name was wrong.
2. **Validate the token first** with `GET /user` — expect 200 + a login.
   This proves scope and validity before any git work fails obscurely.
   If the user pasted the token in chat, tell them to rotate it after
   setup; store it on disk with `chmod 600` in a file OUTSIDE the repo
   (e.g. `$HERMES_HOME/backups/.gh_token`) — never inside the tree being
   committed, and never in memory entries.
3. **Write the backup script** (see template below), keeping secrets and
   volatile files out of the snapshot:
   - EXCLUDE credential stores (`auth.json`), caches, logs, and SQLite
     WAL/SHM sidecars — WAL copies are torn snapshots and leak sessions.
   - INCLUDE the small durable state: configs, memories/notes, skills,
     scheduled-job definitions, small SQLite main files.
4. **Clone with the token in the remote URL** so no credential helper is
   needed: `git clone https://x-access-token:${TOKEN}@github.com/<o>/<r>.git`.
5. **Test twice manually** before scheduling: once with a change (expect a
   commit + push), once with no change (expect silence and exit 0).
   Then verify the remote independently — `GET /repos/<o>/<r>/contents`
   and `/commits` — never trust the script's own exit code alone.
6. **Schedule it as a `cronjob` with `no_agent: true` + `script`** (script
   is the whole job — no LLM cost). Deliver the job to the origin chat so
   failures surface; a silent success sends nothing (see pitfalls).

## Script template

```bash
#!/bin/bash
set -u
TOKEN=$(cat "$TOKEN_FILE")
fail() { echo "BACKUP FAILED: $1"; exit 1; }

[ -d "$REPO/.git" ] || git clone --quiet "https://x-access-token:${TOKEN}@github.com/<o>/<r>.git" "$REPO" || fail "clone failed"
cd "$REPO" || fail "cannot cd"
git config user.name "<bot-name>"          # per-repo: headless hosts have no global identity
git config user.email "<bot>@users.noreply.github.com"
rm -rf data && mkdir -p data               # full replace, not merge — avoids stale files
cp -a <selected paths> data/ || fail "copy failed"
git add -A >/dev/null || fail "git add failed"
git diff --cached --quiet && exit 0         # no changes -> silent success
git commit --quiet -m "backup $(date -u +%Y-%m-%dT%H:%M:%SZ)" || fail "commit failed"
git push --quiet || fail "push failed"
```

## Pitfalls

- **`git add` has no `--quiet`/`-q` option** (unlike `commit`/`push`) —
  it exits 2 with usage text, which a `|| fail` then misreports as a real
  add failure. Redirect instead: `git add -A >/dev/null`.
- **Set `user.name`/`user.email` inside the script (repo-local).** Headless
  containers have no global git identity, so the first commit dies with
  "Author identity unknown" — a failure that only appears on the first
  real change, not at clone time.
- **Empty repo clone only warns** (`You appear to have cloned an empty
  repository`) but exits 0 — treat it as normal, not an error.
- **`no_agent` cron delivers stdout verbatim; empty stdout sends nothing.**
  Design the script to be silent on success and print only on failure —
  otherwise the user gets a message every tick (noise → they mute the
  job). Non-zero exit also raises an error alert, so `fail()` messages
  are the user's only failure signal: make them specific (which step).
- **A token shown in chat is burned** — after wiring the backup, remind
  the user once to revoke and reissue it from GitHub settings.
- **Don't back up volatile runtime state** (locks, pid files, sockets,
  request dumps, `-wal`/`-shm` files): they churn every tick (git noise),
  can't be restored mid-write, and may embed secrets.
