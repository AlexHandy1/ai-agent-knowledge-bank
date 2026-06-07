Start each session by running the /plan-recap skill to understand the project goal and status.

For any coding work:
- Always follow test driven development as outlined in /tdd and /testing.
- Look for opportunities to validate your own work and get feedback directly. Design and run an end-to-end smoke test or practical validation. For projects involving web UI use the /agent-browser skill to review UIs directly.
- Prefer the simplest change that moves things forward. Avoid large refactors or multiple concerns in one go.
- After each logical change:
	1. Check what's staged: `git status`
	2. Commit with a descriptive message: `git commit -m "your message"`
	3. Commit often. Each commit should represent one coherent, working change — not a batch of unrelated edits.

Close each session by running the /summarise-session skill to write up what we did.

This CLAUDE.md provides the framework. Skills contain the detailed guidance. Load skills based on what you're doing:

| Skill | Purpose | Load when |
|-------|---------|-----------|
| plan-recap | Read planning docs and produce a session briefing | Start of every session |
| tdd | RED-GREEN-REFACTOR workflow, recovery strategies | Starting any code work |
| testing | Test patterns, factories, antipatterns | Starting any code work |
| agent-browser | Browser automation for reviewing web UIs | Validating web/UI changes |
| summarise-session | Write a structured session summary to a WORK_SUMMARY file | End of every session |
