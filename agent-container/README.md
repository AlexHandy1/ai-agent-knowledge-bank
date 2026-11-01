# agent-sandbox

A persistent Docker container for running Claude Code (Pro subscription, not API key) autonomously against cloned repos. It's a separate box, environment, and filesystem from the host Mac; isolation comes from that separation (plus a non-root user), not from permission prompts.

Unlike a disposable per-task container, this one is meant to be kept running and built up over time — started and stopped as needed, with tasks sometimes left running unattended (e.g. overnight). Git is the source of truth for code, not the container's filesystem: if the container ever gets corrupted or needs a clean slate, it's fine to destroy and rebuild it, because the real state lives in the target repo, pushed and pulled as usual.

## First-time setup

```bash
cd agent-sandbox
docker compose up -d
```

### 1. Fix volume ownership (only needed once, or if the volume is ever reset)

The `claude-credentials` volume may have been last written by a different user/UID than this container's `agent` user (uid 1001 — Node's official image reserves uid 1000 for its own `node` user, so `agent` lands on 1001). If Claude Code reports `Transcript writes are failing (permission denied — EACCES)` on login, re-own the volume's contents:

```bash
docker run --rm -v claude-credentials:/data alpine chown -R 1001:1001 /data
```

### 2. Claude Code login

The Pro OAuth token is stored on disk (`~/.claude/.credentials.json`) rather than in the OS keychain when running on Linux, so it survives in the `claude-credentials` volume independently of the container itself.

```bash
docker compose exec agent claude
```

Run `/login`, complete the OAuth flow in your browser, then exit (`/exit`). If the volume already has a valid token from a previous setup, this may just work without a fresh login.

### 3. Git identity and credential caching (inside the container, one-time)

```bash
git config --global credential.helper store
git config --global user.name "Your Name (agent-sandbox)"
git config --global user.email "your-email@example.com"
```

`user.name`/`user.email` just set the commit author — they don't affect Claude's `Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>` trailer, which Claude Code still adds to commit messages it makes, same as any other session.

`credential.helper store` caches whatever username/token you enter on the next authenticated git operation into `~/.git-credentials` (plaintext, container-local, not in `/workspace` so it can never be picked up by `git add`). Public repos don't prompt for this at all on `clone` — GitHub allows anonymous read access to public repos over HTTPS; you'll only see the prompt on your first `push` (write access always requires auth, public or private).

### 4. Per-repo: create a scoped PAT

For each repo this container will work on, create a **fine-grained** GitHub PAT at [github.com/settings/personal-access-tokens](https://github.com/settings/personal-access-tokens), scoped to **only that repository**, with `Contents: Read and write`, `Metadata: Read-only`, and `Pull requests: Read and write`. A repo-scoped, revocable token is used instead of mounting your real SSH key, per the sandboxing report's guidance.

## Day-to-day use

```bash
docker compose up -d        # starts the container if it isn't already running
docker compose exec agent bash
```

**First time working with a given repo**, inside the container:

```bash
cd /workspace
git clone https://github.com/<owner>/<repo>.git <repo>
cd <repo>
# install that repo's own dependencies, e.g.:
python3 -m venv venv && source venv/bin/activate && pip install -r requirements.txt
```

The first `git push` to a private repo (or any repo the container doesn't yet have a cached token for) will prompt for a username and password — use the GitHub username and the repo-scoped PAT from step 4 as the password. It's cached from then on.

**Run Claude Code autonomously:**

```bash
claude
```

**Make a change and open a PR**, same as any normal git workflow:

```bash
git checkout -b <branch-name>
# ... changes, via Claude or by hand ...
git add <files>
git commit -m "<message>"
git push -u origin <branch-name>
```

`git push` prints a direct "Create a pull request" URL for the branch — open it in your Mac's browser to open the PR. (`gh` CLI isn't installed in the image, so this is the path for now.)

**Stop / restart** — the container and everything in it (clones, venvs, cached git credentials) persists as long as the container exists, independent of whether it's running:

```bash
docker compose stop     # stops the container, keeps its filesystem intact
docker compose up -d    # brings the same container back
docker compose exec agent bash
```

## Known limitations (first pass)

- No network allowlist/proxy — the container has open egress. Fine for a solo-dev box per the sandboxing report, but revisit before this setup is reused somewhere more exposed.
- `~/.claude.json` (trusted-dir state, etc.) is not preserved if the `claude-credentials` volume is ever reset — only `.credentials.json` is. Claude Code will regenerate it and you may need to re-approve trust prompts once. Not worth solving until it's actually a recurring friction point.
- Git identity is your own GitHub account (same PAT-authenticated account, just a different `user.name` label) — not a separate bot identity. Revisit with a GitHub App if a real separate identity is ever needed.
- `gh` CLI isn't installed, so opening a PR means following the URL `git push` prints into a browser rather than `gh pr create` from inside the container.
