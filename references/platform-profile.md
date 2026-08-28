# Platform Profile

Read this file before operating a new or changed learning platform. Build a small evidence-backed profile in memory; do not copy personal data into the skill.

## Required profile fields

```yaml
platform_host: visible hostname
project_identity:
  stable_id: null
  title: null
  disambiguators: []
course_identity:
  stable_id: null
  title: null
subitem_types: [video, document, assessment]
completion_signal:
  countdown_text: null
  countdown_selector: null
  explicit_complete_text: []
anti_idle_text: []
rate_control:
  visible_values: []
  selected_value_signal: null
project_metrics:
  exact_task_count: null
  points: null
  rounded_percent: null
known_sync_delay_seconds: null
```

Prefer accessible names, visible text, and roles. Use CSS selectors only as a fallback and revalidate them after a platform update.

## Yunxuetang-style profile observed in this workflow

Treat these as starting hints, not permanent truths:

- Project pages may contain duplicate project titles. Disambiguate with the stable project ID, exact task count, recent-course name, and progress; do not rely on title alone.
- A course can expand into child rows such as `视频 | ...` and `考试 | ...`.
- The effective completion message can resemble `还需 35分钟 3秒 可完成本课程学习`. A useful fallback container is `.yxtulcdsdk-course-player__countdown`.
- Ordinary anti-idle prompts can contain `继续学习`, `继续播放`, or `继续观看`.
- The player can be JW Player-based. Fallback controls may include `.jw-icon-playrate`, `.jw-menu-playrate li`, and `.jw-playrate-label`; the real media element is the visible `video` with a non-zero duration.
- Verify playback using `paused`, `playbackRate`, current time, and duration when the host browser tool exposes those read-only properties.
- Ordinary assessment options may use radio and checkbox labels. Fallback selectors observed here include `label.yxtulcdsdk-ques-radio` and `label.yxtulcdsdk-ques-checkbox`.
- Completion icons and rounded percentages are secondary evidence. Exact `completed/total` task counts and result pages are stronger.
- A `网络异常` dialog may offer `刷新重试`. After recovery, verify the URL/task ID and visible course title before resuming; a stale card index can select a neighboring course.

## Profile validation checklist

- The selected project matches at least two disambiguators.
- The chosen course is unfinished or partially complete.
- Exactly one playable media element is active.
- The displayed and measured playback rates agree.
- The countdown has been observed and is decreasing.
- The task count and points were captured before the run.
