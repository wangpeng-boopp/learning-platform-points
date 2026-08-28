# Learning Platform Points

A portable Agent Skill for operating an authorized online-learning platform efficiently while preserving the platform's real completion, attendance, and risk-control rules.

## What it does

- Resumes the current or shortest eligible unfinished course.
- Uses the highest playback speed exposed by the normal interface.
- Handles ordinary `继续学习` / continue-watching prompts.
- Waits for the effective learning countdown to finish and disappear before advancing.
- Completes permitted low-stakes course checks when automatic submission is authorized.
- Verifies server-visible task and point changes, with checkpoints for delayed credit or interrupted sessions.

## Safety boundaries

The skill keeps exactly one course stream active and never modifies timers, injects completion state, uses unsupported speeds, or attempts to evade concurrency, identity, proctoring, attendance, or risk controls. It stops for CAPTCHA, OTP, face/voice verification, payment, legal attestations, and high-stakes assessments.

## Install

### WorkBuddy

1. Install and connect [Tencent BrowserSkill](https://github.com/Tencent/BrowserSkill) (`bsk` CLI plus its Chrome or Edge extension).
2. Import this folder as a Skill in WorkBuddy, keeping `SKILL.md` at the folder root.
3. Start from an already signed-in learning-platform tab and invoke the skill with an explicit goal, such as:

   > Continue my authorized learning project. Use the highest visible allowed rate, handle ordinary continue-learning prompts, auto-submit permitted low-stakes course checks, and stop only when no eligible work remains or I must intervene.

See [`references/workbuddy-adapter.md`](references/workbuddy-adapter.md) for the browser-capability mapping.

### Codex or another Agent Skills host

Install or copy the folder into the host's Skills directory, then invoke `learning-platform-points` for an authorized learning run. The generic workflow is in [`SKILL.md`](SKILL.md).

## Repository structure

```text
learning-platform-points/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── assessment-workflow.md
    ├── platform-profile.md
    ├── recovery-and-checkpoints.md
    └── workbuddy-adapter.md
```

## Notes

- Platform-specific selectors and reward behavior must be learned from visible evidence; do not guess them.
- Public documentation can describe concepts, but the signed-in platform remains the authority for completion and credit.
- Passing is enough unless another attempt is genuinely required for credit.
