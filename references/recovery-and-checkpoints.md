# Recovery and Checkpoints

Use checkpoints to make a long browser workflow resumable and to prevent duplicate course starts or exam submissions.

## Minimal checkpoint

```json
{
  "run_target": {"type": null, "value": null},
  "course_ordering": null,
  "auto_submit_authorized_for_run": false,
  "assessment_eligibility": "unknown",
  "project_id": null,
  "project_title": null,
  "course_id": null,
  "course_title": null,
  "subitem": null,
  "countdown_seen": false,
  "last_countdown_seconds": null,
  "playback_rate": null,
  "course_status": "unknown",
  "exam_status": "not_seen",
  "exam_score": null,
  "exam_attempt": null,
  "submission_pending": false,
  "submission_started_at": null,
  "pre_submit_attempt_count": null,
  "assessment_attempt_id": null,
  "tasks_before": null,
  "tasks_after": null,
  "points_before": null,
  "points_after": null,
  "credit_pending": false,
  "credit_pending_since": null,
  "last_verified_at": null,
  "retry_count": 0,
  "last_error": null
}
```

Store no cookies, tokens, phone numbers, employee IDs, personal names, or other unnecessary account data.

## Reconnection rules

1. Re-observe before acting. Do not repeat the last click blindly.
2. Reclaim or reopen the exact course using a stable project/course ID when possible.
3. Reconcile the checkpoint with server-visible status. If the task is already complete or an exam result exists, advance instead of repeating it.
4. Confirm the visible course title and stable ID match; DOM list indices can change after reload.
5. Restore playback through the visible controls and verify rate, pause state, and countdown movement.

## Network and blank-page recovery

Use a visible dialog retry as the initial recovery action. If it fails, wait 10, 30, then 60 seconds before at most three additional reload or same-course re-entry attempts. This makes the total budget unambiguous.

- On a visible network dialog, use its normal retry control once and wait for the page to stabilize.
- If the player becomes blank or remains a skeleton, reload or return to the project page and re-enter the same course.
- An absent countdown on a blank or loading page is never completion.
- If resources remain unavailable after the retry budget, preserve the checkpoint and stop the active run. If the user requested monitoring, use the host's supported scheduler to retry later.

## Submission recovery

Assessment submission is not idempotent. When the response is uncertain:

1. If `submission_pending` is true, do not click submit.
2. Wait for the result page using the configured bounded polling schedule.
3. Check exam history, score, pass state, and attempt count against `pre_submit_attempt_count`.
4. Submit again only if the platform proves the prior attempt was not recorded and another attempt is safe.

## Credit synchronization recovery

When a task completes but points have not changed, set `credit_pending` and poll using the configured 5, 15, 30, and 60 second waits or a validated platform-specific delay. Clear it only after the server-visible points update or the platform proves that this task has no point reward. If still unresolved, stop or schedule a recheck instead of predicting credit or doing unnecessary extra courses.

## User-only blockers

Stop immediately for login expiration requiring credentials, CAPTCHA, slider, OTP, face/voice verification, identity attestation, concurrent-session warnings, or a platform message requiring the learner personally. Report the exact user action required without exposing private data.
