# WorkBuddy Adapter

This package follows the open Agent Skills `SKILL.md` layout. Import the entire `learning-platform-points` folder into a WorkBuddy version that supports Skill import.

WorkBuddy's published documentation currently describes both a standard `SKILL.md` import flow and an older desktop flow that generates a WorkBuddy-specific Skill. Do not invent an undocumented `skill.yml` schema. If the installed desktop version does not accept this folder directly, ask WorkBuddy to create a local Skill from this `SKILL.md` and keep the reference files alongside it.

## Install the official browser dependency

The workflow requires Tencent BrowserSkill. It is a separate official Skill and extension that can reuse a real Chrome or Edge login session.

On Windows PowerShell, follow Tencent's current official installation guide. The documented commands are:

```powershell
irm https://raw.githubusercontent.com/Tencent/BrowserSkill/main/install.ps1 | iex
bsk install-skill --yes
bsk doctor
```

Install BrowserSkill's own Chrome or Edge extension and continue only when `bsk doctor` reports every required check as `ok` or `na`. Do not assume a Codex, Claude, or other browser extension can replace it.

Official references:

- BrowserSkill repository: <https://github.com/Tencent/BrowserSkill>
- BrowserSkill installation: <https://raw.githubusercontent.com/Tencent/BrowserSkill/main/AGENT_INSTALL.md>
- Agent Skills format: <https://github.com/agentskills/agentskills/blob/main/docs/specification.mdx>
- WorkBuddy Skill import documentation: <https://www.workbuddy.ai/docs/zh/ide/Features/Skills>
- WorkBuddy desktop custom Skill flow: <https://www.workbuddy.ai/docs/workbuddy/From-Beginner-to-Expert-Guide/Practice-Cases/Create-Skills>

## Required capabilities

| Capability | Required behavior |
| --- | --- |
| Reuse signed-in browser | Attach to the user's existing authorized session without requesting or exporting credentials. |
| Inspect visible state | Read the current URL, title, accessible text, visible dialogs, and course metrics. |
| Interact visibly | Click, type, and choose controls through the rendered interface. |
| Wait and re-observe | Pause for bounded intervals and verify the outcome of each state-changing action. |
| Verify media state | Read playing/paused state and selected playback rate, or infer them from reliable visible player indicators. |
| Recover navigation | Reload, go back, reclaim a tab, or re-enter a course by stable identity. |

Optional capabilities improve reliability:

- A search or research worker for course material. It must not control the learning tab.
- A scheduler or heartbeat for multi-hour runs. The skill itself is not a resident background process.
- A small persistent key-value or file store for the checkpoint in `recovery-and-checkpoints.md`.

## BrowserSkill lifecycle

1. Start one BrowserSkill session.
2. If the course is already open in the user's normal browser, explicitly borrow that tab. Otherwise use BrowserSkill's Agent Window.
3. Use `observe` or `snapshot` for ordinary page understanding and semantic interactions.
4. Use `evaluate` only when the visible snapshot cannot reliably expose media pause state, playback rate, countdown text, or a normal dialog. Never read cookies, local storage, authorization headers, or unrelated private data.
5. Use normal BrowserSkill actions such as navigate, click, hover, select, press, and bounded waits.
6. For login, OTP, CAPTCHA, slider, or identity verification, use BrowserSkill's `request-help` path and stop until the user finishes.
7. At the terminal condition, return a borrowed tab and stop the BrowserSkill session.

## Host mapping procedure

1. Import this folder without changing `SKILL.md` semantics.
2. Confirm BrowserSkill is installed and healthy.
3. Test on one course in observe-only mode first: identify the project, course, rate control, countdown, task count, and points without clicking.
4. Run one authorized course and verify the completion gate and server-side task delta.
5. Test recovery from a paused player and a normal anti-idle prompt.
6. Verify a borrowed tab is returned cleanly at the end.
7. Only then enable a longer bounded run.

WorkBuddy's public automation guide documents hourly/daily/weekly/one-time schedules, but not a guaranteed minute-level heartbeat. A long run can continue within one bounded task; crash recovery or frequent wakeups require the scheduler available in the installed version or an external supervisor. The Skill itself is not a background daemon.

If WorkBuddy cannot load standard `SKILL.md` folders, use the same instructions as a system/workflow prompt and keep the reference files alongside it. Do not replace the browser layer with timer injection, direct completion APIs, or undocumented risk-control workarounds.
