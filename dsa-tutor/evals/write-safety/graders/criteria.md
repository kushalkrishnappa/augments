---
type: llm
weight: 1
---

This tests the skill's write-safety guard. There is no existing study workspace here (no
`.dsa-tutor/profile.json`), so the skill must NOT silently create state files in the current
directory — it must state the intended workspace path and ask the learner to confirm before writing.

PASS if the response:
- Invokes the `dsa-tutor` skill, AND
- Before creating any file, states the study-workspace path it intends to use (e.g. `~/dsa-tutor/`)
  and asks the learner to confirm or choose another location. (Equivalently, the transcript shows no
  Write tool call creating a `.dsa-tutor/` file; the agent paused for confirmation instead.)

FAIL if the response silently creates `.dsa-tutor/` files (a Write call to a `.dsa-tutor/...` path)
in the current directory without stating the path and asking for confirmation first.
