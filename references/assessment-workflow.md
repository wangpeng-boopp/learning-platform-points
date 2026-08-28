# Assessment Workflow

Use this workflow only for an eligible assessment defined in `SKILL.md`.

## Prepare

1. Read the assessment instructions, question count, score, passing threshold, attempt limit, lockout risk, and whether unanswered or partially selected multi-choice items receive credit.
2. If attempts are limited or failure can lock the course, increase the evidence threshold and do not guess merely to keep the run moving.
3. Start the assessment only when the user has authorized automatic submission and the course content or reliable sources are available.
4. Confirm the page context and instructions are consistent with an ordinary low-stakes course check. A generic request to auto-submit all exams does not override signs of proctoring, identity verification, licensing, compliance-critical, or safety-critical assessment.

## Capture the actual attempt

After the attempt starts, capture every visible question and option. Do not assume a prior attempt's option order or question set is unchanged.

For each item, record internally:

```yaml
question_number: 1
type: single | multiple
question: visible text
options: visible labels and text
proposed_answer: []
evidence: short rationale
confidence: high | medium | low
```

Do not extract hidden answer keys, inspect private application state for correct-answer flags, or manipulate scoring.

## Evidence order

Use evidence in this order:

1. The course's visible video, transcript, captions, slides, or notes.
2. The course provider's official description or source material.
3. Primary or authoritative external references.
4. A clearly labeled inference from the course framework.

When research can run concurrently, delegate only the research. The single browser operator remains the only worker allowed to select options or submit.

## Answer and submit

1. Distinguish single-choice from multiple-choice controls.
2. Select answers through visible controls and verify the answered-item count equals the question count.
3. Resolve low-confidence items with further evidence when time and attempt limits allow.
4. Record the pre-submit attempt count and timestamp, set `submission_pending: true`, then submit once. Submission is non-idempotent.
5. If the click result is delayed or uncertain, poll for a result using the configured 5, 10, then 20 second waits and inspect the result or exam-history page before attempting another submission.
6. If the poll budget ends without a definitive result, preserve `submission_pending` and stop or schedule a recheck. Do not submit again merely because the response timed out.

## Verify the result

- Read the stable result page for `passed/failed`, score, and attempt count.
- Clear `submission_pending` only after the platform proves the result or proves that no attempt was recorded.
- Confirm the project task count or course status updates after a pass.
- If passed, stop; do not retake for a higher score when there is no additional reward.
- If failed and passing is required for credit, retry only when the attempt budget permits, there is no lockout risk, and new evidence or visible feedback materially changes the answers.
- Never loop through attempts blindly.
