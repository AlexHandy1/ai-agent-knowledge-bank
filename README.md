# AI Agent Knowledge Bank

A personal collection of agentic skills, workflows, and resources I use — a mix of custom ones I've built and links to other resources.

The skills and scaffolding here are primarily designed around [Claude Code](https://claude.ai/code), though the patterns and ideas are transferable to other agentic setups.

## Install

A subset of the skills and workflows I currently use as outlined in the `CLAUDE.md` file are available to install into any project. Clone this repo, then from your project root run:

```bash
mkdir -p .claude && cp -r ~/ai-agent-knowledge-bank/skills .claude/ && cp ~/ai-agent-knowledge-bank/CLAUDE.md .
```

> Adjust the path if you cloned the repo somewhere other than `~`.

This copies all skills into your project's `.claude/skills/` directory (so they're available in cloud/autonomous sessions) and drops `CLAUDE.md` at your project root.

> I'm still exploring the best way to package and share this as a proper Claude Code plugin — will update once the workflow has stabilised.

---

## Install contents

| Skill | Trigger | Description | Source |
|-------|---------|-------------|--------|
| [plan-recap](skills/plan-recap/SKILL.md) | `/plan-recap` | Reads planning docs and produces a concise session briefing | Custom |
| [summarise-session](skills/summarise-session/SKILL.md) | `/summarise-session` | Writes a structured `WORK_SUMMARY_DDMMYY.md` from the current session | Custom |
| [daily-summary](skills/daily-summary/SKILL.md) | `/daily-summary` | Pulls Granola meeting notes and writes a dated daily summary with action items | Custom |
| [daily-plan](skills/daily-plan/SKILL.md) | `/daily-plan` | Builds the next day's `to_do_DDMMYYYY.md` from unfinished tasks and meeting actions | Custom |
| [grill-me](skills/grill-me/SKILL.md) | `/grill-me` | Stress-tests a plan by interviewing you until reaching shared understanding | [Matt Pocock](https://github.com/mattpocock/skills) |
| [tdd](skills/tdd/SKILL.md) | `/tdd` | Red-green-refactor TDD workflow with vertical slices | [Matt Pocock](https://github.com/mattpocock/skills) |
| [testing](skills/testing/SKILL.md) | `/testing` | Test patterns, factories, and anti-patterns for behaviour-driven tests | [Gidi Morris](https://github.com/gmmorris/xp-and-lean-agent-specification) |
| [agent-browser](skills/agent-browser/SKILL.md) | `/agent-browser` | Browser automation CLI for AI agents via CDP | [Vercel Labs](https://www.skills.sh/vercel-labs/agent-browser/agent-browser) |

---

## Custom skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| [plan-recap](skills/plan-recap/SKILL.md) | `/plan-recap` | Reads planning and work summary docs from the current project and produces a concise session briefing |
| [summarise-session](skills/summarise-session/SKILL.md) | `/summarise-session` | Reviews the current conversation and writes a structured summary to a `WORK_SUMMARY_DDMMYY.md` file |
| [daily-summary](skills/daily-summary/SKILL.md) | `/daily-summary` | Pulls today's Granola meeting notes and writes a dated daily summary with consolidated action items |
| [daily-plan](skills/daily-plan/SKILL.md) | `/daily-plan` | Builds the next working day's `to_do_DDMMYYYY.md` by carrying over unfinished tasks and pulling in meeting actions |

## Skills from others

| Skill | Source | Description |
|-------|--------|-------------|
| agent-browser | [skills.sh](https://www.skills.sh/vercel-labs/agent-browser/agent-browser) | Browser automation CLI for AI agents — fast, persistent browser control with accessibility-tree snapshots and session management |
| xp-and-lean-agent-specification | [GitHub](https://github.com/gmmorris/xp-and-lean-agent-specification) | Gidi Morris's XP and lean principles applied to agentic workflows — skills and specifications for structured, disciplined AI-assisted development |
| skills | [GitHub](https://github.com/mattpocock/skills) | Matt Pocock's collection of Claude Code skills |
| gstack | [GitHub](https://github.com/garrytan/gstack) | Garry Tan's personal AI development stack and tooling setup |
| gbrain | [GitHub](https://github.com/garrytan/gbrain) | Garry Tan's gbrain — an architectural pattern for agentic memory and reasoning worth exploring |

## Plugins from others

[Claude Code plugins](https://claude.com/plugins) are a way to package multiple skills, agents, and workflows together into a single installable unit — useful for shipping opinionated setups for a given domain or workflow.

| Plugin | Description |
|--------|-------------|
| [frontend-design](https://claude.com/plugins/frontend-design) | Create distinctive, production-grade frontend interfaces with high design quality |
| [feature-dev](https://claude.com/plugins/feature-dev) | End-to-end feature development workflow — from spec to working, tested code |

I'm exploring how to package opinionated plugin packs for specific workflows (e.g. data analysis) — bringing together the right skills, agents, and scaffolding in one place.

## Learnings & gotchas

Collection of practical tips I've learnt as I go and gotchas to watch out for. See [LEARNINGS_AND_GOTCHAS.md](LEARNINGS_AND_GOTCHAS.md) for the full list.

| Topic | Summary |
|-------|---------|
| [Skills in cloud sessions](LEARNINGS_AND_GOTCHAS.md#skills-are-not-available-in-cloudautonomous-sessions-unless-committed-to-the-repo) | Project-scope your skills if you want them available in cloud-based autonomous sessions — user-level skills live on your machine and won't be cloned |

## Other helpful resources

| Resource | Description |
|----------|-------------|
| [Claude Code Mastery](https://arps18.github.io/posts/claude-code-mastery/) | Practical guide to getting the most out of Claude Code, including a curated list of popular CLAUDE.md files worth studying |
| [Working with AI](https://eugeneyan.com//writing/working-with-ai/) | Eugene Yan's reflections on day-to-day AI-assisted work — patterns, pitfalls, and how to stay in the driver's seat |
| [autoresearch](https://github.com/karpathy/autoresearch) | Andrej Karpathy's autonomous research agent — a reference implementation for multi-step agentic research workflows |
| [Levels of Agentic Engineering](https://www.bassimeledath.com/blog/levels-of-agentic-engineering) | A framework for thinking about how much autonomy to hand to AI agents at different stages of a project |
| [Agentic Engineering Patterns](https://simonwillison.net/guides/agentic-engineering-patterns/) | Simon Willison's guide to recurring patterns in agentic systems — practical and grounded in real-world experience |
