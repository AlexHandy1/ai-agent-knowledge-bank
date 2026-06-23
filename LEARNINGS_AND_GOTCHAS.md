# Learnings & Gotchas

A collection of practical tips I've picked up along the way and gotchas to watch out for.

---

## Skills are not available in cloud/autonomous sessions unless committed to the repo

When you run a cloud-based autonomous session (e.g. via claude.ai tasks or remote agents), Claude starts from a fresh clone of whatever repo you point it at. Only what's committed to that repo is available.

**What IS available in cloud sessions:**

Anything in the cloned repo is available, including:
- `CLAUDE.md`
- `.claude/settings.json` hooks
- `.mcp.json` MCP servers
- `.claude/rules/`, `.claude/skills/`, `.claude/agents/`, `.claude/commands/`

**What is NOT available:**

- Your user-level `~/.claude/CLAUDE.md` — lives on your machine, not in the repo
- Your user-level `~/.claude/skills/`, `~/.claude/agents/`, `~/.claude/commands/`

**Practical implications:**

- Skills stored locally or referenced externally (like this bank) won't be available to cloud sessions unless you commit them into the target repo under `.claude/skills/`
- If you want skills available across cloud tasks, commit them into each repo's `.claude/` directory, or enable them on claude.ai itself (skills enabled on claude.ai are loaded into cloud sessions automatically)

The "skills you enable on claude.ai are loaded automatically" note is worth exploring as a way to make your skills available without committing them to every repo.

---

## Agent SDK billing is moving to plan-based (from June 15 2025)

Until mid-June 2025 the recommended pattern for using Claude agents in cloud/remote setups was to authenticate with a separate, directly-billed API key — which meant a mixed pattern: API key in cloud, Claude plan locally.

From 15 June 2025 onwards you can use your existing Claude plan to authenticate the Agent SDK, removing the need for a separate API key. This applies to both Claude Code agents and Anthropic SDK-based agents.

**Reference:** [Use the Claude Agent SDK with your Claude plan](https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan)

**Practical implication:** If you currently use a mixed authentication pattern, migrate to plan-based auth once the rollout lands so local and cloud sessions behave consistently.

---

## Visual browser review is not available in Claude Code web

The `/agent-browser` skill (and any Chrome-based validation) does not work in Claude Code web sessions. Chrome downloads are blocked by the environment's network policy, so UI checks must be done via other means (e.g. `TestClient`, `curl`, or manual inspection).

**Practical implication:** If your workflow relies on `agent-browser` for automated UI validation, run those sessions locally in the desktop/CLI app rather than through claude.ai/code.

---

## Fable 5 is capable enough to build non-trivial apps end-to-end in a single session

First experiment with Fable 5: built a personal wiki (inspired by [karpathy's reading-list gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) and [rohitg00's wiki gist](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2)) from ~500 historic articles in ~10 minutes. The output included a fully functional update system and a browsable front-end — no hand-holding required.

**Practical implication:** Fable 5 is worth reaching for on ambitious single-session builds.

---

## Granting Claude Code web access to a private GitHub repo

When connecting a private repo to Claude Code web, the GitHub OAuth connection alone is not enough. You also need to explicitly grant the Claude GitHub app access to the specific repo.

Go to: **https://github.com/apps/claude/installations/select_target**

After 2FA, you can select which repositories the Claude app can access. Add the private repo there and it will become available to Claude Code web sessions.

---

## Autonomous sessions are structurally more token-hungry than interactive ones

Autonomous sessions consume tokens faster than interactive ones for two reasons: the model has no human checkpoints to catch and halt wasteful trajectories, and it appears to more readily spawn greater multiples of parallel sub-agent paths, so consumption compounds quickly.

Task budgets exist as a partial mitigation — they allow setting token ceilings — but are scoped to the API and not available on the Claude plan: https://platform.claude.com/docs/en/build-with-claude/task-budgets

**Practical implication:** Treat autonomous runs as a higher-cost mode by default. Set conservative scope, be explicit about what the agent should *not* do, and consider defining a stopping condition for more open-ended tasks. In-process budget awareness is an open area: injecting a token/time budget into the system prompt and asking the agent to self-report at a threshold is one low-tech approach worth exploring.

---

## Model and effort level compound significantly on longer tasks

The combination of model tier and effort level creates a very wide cost range. Choosing Opus at `/xhigh` vs Sonnet at `/low` for the same task can produce radically different token consumption. This is particularly significant on long-running tasks where that multiplier applies across many calls.

There is currently no tooling to tell you which combination a task warrants before you run it, and no transparency into how different combinations map to tiered plan limits. Empirical experiments are needed to build intuition.

**Practical implication:** Default to a conservative model/effort combination and escalate only if quality is demonstrably insufficient.

---

## Significant gap in pre-task cost visibility

Two related gaps: (1) there is no way to estimate what a task will cost before running it; (2) there is no mapping from estimated cost to plan tier limits, so you cannot answer "will this task be possible within my limits?" without just trying.

This is an area actively under investigation. One interesting early approach is collecting usage and cost data post-session to build up predictions over time: https://github.com/CodeSarthak/tarmac

---

## TDD and testing patterns are not yet reliable for autonomous sessions

TDD and testing pattern adherence is patchy in practice. Common gaps observed:

- Rarely runs one test at a time in a true red-green cycle — tends to write several tests then implement, or implement first and backfill tests
- Will not independently design repeatable integration or smoke tests without being asked
- For front-end changes especially, test-first discipline is unlikely to emerge without explicit prompting

**What does work well:** When the `/agent-browser` skill is loaded with guidance to validate post-build, it reliably runs through visual validation autonomously — browsing, navigating, screenshotting, and reading the result. This closes the development loop effectively even without formal tests.

**Practical implication:** Do not rely on autonomous sessions to self-enforce TDD rigour. Continue to investigate what guardrails and harness adjustments (CLAUDE.md instructions, hooks, skill updates) are needed to make this work more consistently before using in higher-stakes autonomous workflows.

---

## Write incrementally on longer tasks to survive budget limits

Until better cost/limit controls exist, structure longer autonomous tasks to commit or checkpoint work frequently rather than accumulating everything in memory until the end. If the session hits a budget ceiling mid-task, incremental commits mean you have recoverable progress to build from rather than starting over.

A related pattern in Claude Code web: instruct the agent to pause before it expects to hit limits and raise a PR with whatever is complete. This gets work out of the session and into a reviewable state before the session is blocked, and gives a clean handoff point for continuing in a new session.

---

## Architecture and documentation files are often skipped without explicit instruction

Observed that agents will frequently skip reading `ARCHITECTURE*.md` and `README` files even when they are present and clearly relevant — they proceed straight to coding without building an understanding of the system first. This leads to changes that miss important context or contradict documented decisions.

**Practical fix:** Be maximally explicit in `CLAUDE.md`. Adding a dedicated instruction — "Always look for and read any ARCHITECTURE*.md files or README files across the project (including in subdirectories and modules) — make sure you review documentation and understand the codebase before you proceed" — appears to address this in initial testing.

**Status:** Anecdotal testing supports the fix working, but this warrants more systematic experimentation to confirm reliability across different task types and session modes.

**Related:** [[CLAUDE.md architecture reading instruction]]
