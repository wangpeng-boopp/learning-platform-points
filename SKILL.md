---
name: learning-platform-points
description: Operate a user-authorized online learning platform to resume eligible courses, use the highest visible allowed playback speed, handle ordinary visible continue-learning prompts, complete permitted low-stakes quizzes, verify server-side credit, and recover interrupted sessions. Use only when the user asks to run, resume, or optimize course learning and the platform or organization permits automation; never use it to evade attendance, concurrency, identity, proctoring, or risk controls.
license: MIT
---

# Learning Platform Points

Run an authorized learning project continuously and efficiently while preserving the platform's real completion rules.

## Load only what is needed

- Read [references/platform-profile.md](references/platform-profile.md) before operating a new or changed platform. Update the in-memory profile from visible evidence; do not guess selectors or project identity.
- Read [references/assessment-workflow.md](references/assessment-workflow.md) only when an assessment appears.
- Read [references/recovery-and-checkpoints.md](references/recovery-and-checkpoints.md) when a run will span multiple turns, when scheduling is requested, or after any browser, network, login, or submission interruption.
- Read [references/workbuddy-adapter.md](references/workbuddy-adapter.md) when running this skill in WorkBuddy or another host whose browser tools differ from the generic capability names below.

## Authorization and eligibility

Proceed only in a browser session the user owns and has authorized this agent to operate.

- Ordinary course navigation, playback, visible speed selection, and acknowledging an ordinary visible prompt to continue the current learning media are in scope only when the user explicitly asks to run or resume learning and such automation is permitted by the platform or organization.
- Submit assessments automatically only when the user has explicitly enabled automatic submission for the current task or continuing run. Do not treat an old or unrelated session as permanent authorization.
- Eligible assessments are ordinary, open-book, low-stakes course checks for which assistance is allowed. Do not answer or submit proctored, identity-verified, licensing, credentialing, admissions, compliance-critical, or safety-critical assessments.
- Stop for CAPTCHA, slider, face/voice verification, SMS/OTP, payment, legal attestation, or a prompt that only the user can truthfully complete.

## Non-negotiable constraints

- Keep exactly one course media stream active. Research may run in parallel, but research workers must not control the learning tab or start another course.
- Use only the highest playback speed exposed by the normal interface. Never modify timers, inject completion state, replay APIs, hide playback, force unsupported speeds, use multiple accounts/browsers to evade concurrency checks, or bypass monitoring and risk controls.
- By default, prefer a partially completed course; otherwise choose the shortest eligible unfinished course by estimated real completion time, including its remaining media, required subitems, and likely assessment cost. A user-specified ordering overrides this default.
- Do not repeat a completed course unless visible before-and-after evidence proves that repetition produces new credit and the platform permits it.
- Do not assume only courses with exams earn points. Learn reward behavior from exact task-count and point deltas.
- Never treat a click, player end event, or a temporarily absent countdown during page load as proof of completion.

## Run configuration

Infer safe defaults when the user does not specify them:

```yaml
project: current authorized project
preferred_rate: 2
single_stream: true
auto_submit_assessments: false unless explicitly enabled
passing_is_enough: true
max_assessment_attempts: 1 unless a retry is necessary for credit and is safe
network_retry_delays_seconds: [10, 30, 60]
submission_result_poll_seconds: [5, 10, 20]
credit_sync_poll_seconds: [5, 15, 30, 60]
stop_when: user target reached, no eligible work remains, or user intervention is required
```

If the user specifies a point target, task target, time limit, named project, or course ordering, it overrides the corresponding default. Treat a numeric point target as `visible server points >= target`; do not require an exact equality or count predicted rewards. Do not invent a broader target.

## Operating state machine

Maintain one current state and make browser actions through visible, ordinary controls.

### 1. Observe and restore

1. Reuse the user's existing signed-in browser session.
2. Inspect open tabs and identify the exact project using stable IDs when available, then title, progress, and recent-course evidence.
3. Read current exact task count, visible points, current course, subitem, and completion state before changing anything.
4. If a checkpoint exists, reconcile it with server-visible state. Server state wins.

### 2. Select or resume

Unless the user explicitly chooses another ordering, use this priority order:

1. Current course with valid saved progress.
2. Shortest unfinished course, using remaining countdown when available.
3. For unstarted courses, inspect read-only card or outline metadata and estimate `displayed duration / allowed speed + required subitems + assessment cost`.
4. When reward values are known, prefer the best confirmed reward per estimated minute; otherwise prefer the shortest eligible course.
5. Break ties by fewer subitems, then confirmed credit eligibility.

After starting a course, stay with it until completion or a genuine unrecoverable interruption.

### 3. Start and verify playback

1. Start or resume through the visible course control.
2. Select the greatest speed value actually exposed by the interface that is less than or equal to `preferred_rate`. If no exposed value satisfies the limit, keep the safe platform default rather than inventing a value.
3. Verify the real media element or player state shows playing and the selected rate.
4. Observe at least one valid learning countdown and set `countdown_seen: true` before completion can be inferred.
5. Confirm the countdown decreases over two observations. If it does not, check pause state, visible continue-learning prompts, player loading, and network state.

### 4. Monitor

Poll often enough to catch prompts without creating noisy or overlapping actions.

- When an ordinary visible prompt asks the learner to continue or resume the current media, acknowledge it once through the normal interface, using the current platform profile rather than a fixed-language phrase list. Then verify the prompt is gone, playback is active, the rate is correct, and the countdown resumes decreasing. Do not preempt or suppress the prompt, and do not treat identity, attestation, or proctoring requests as ordinary continuation.
- If the rate falls, restore it through the visible rate menu.
- If media pauses without an identity or risk-control challenge, resume it and verify state.
- Record checkpoints after meaningful transitions, not after every poll.

### 5. Apply the completion gate

The platform's effective learning countdown is the primary completion signal.

1. Require `countdown_seen: true` for the current subitem.
2. While the countdown is above zero, keep monitoring.
3. When the countdown first reaches zero in the current UI language, do not navigate away.
4. Wait for at least two separated observations in which the countdown element is completely absent, or accept an equally explicit server-visible completed state.
5. Confirm the server-visible course/subitem state changed. If the course has multiple videos, documents, or embedded checks, continue to the next required subitem.

An absent countdown on an unloaded, blank, skeleton, or error page is not completion.

### 6. Complete an eligible assessment

If an eligible assessment exists, follow [references/assessment-workflow.md](references/assessment-workflow.md). If it is not eligible or automatic submission is not authorized, stop and report what is needed.

### 7. Verify credit and continue

After the course and any required assessment:

1. Wait for the result and project state to stabilize.
2. Re-read exact completed-task count, course status, and visible points.
3. Prefer exact counts over rounded project percentages.
4. Record task and point deltas separately. A task increment does not necessarily mean an immediate point increment.
5. If the task changes but points do not, poll using `credit_sync_poll_seconds` or a validated platform-specific delay and mark `credit_pending` while waiting.
6. If credit is still pending after the budget, preserve the checkpoint and recheck later. Do not perform extra courses merely to compensate for an unconfirmed delayed reward unless the user explicitly accepts that throughput tradeoff.
7. If passed, do not retake merely to improve the score.
8. Check the stopping condition; otherwise select the next eligible course.

## Stop and report

Stop safely when the requested target is reached, no new eligible credit-bearing work remains, the configured retry budget is exhausted, or user-only action is required.

Report only operationally useful facts: completed courses, verified score/pass state, exact task/point deltas, the current checkpoint, and any user action needed. Never expose cookies, tokens, account identifiers, private profile data, or hidden page state.
