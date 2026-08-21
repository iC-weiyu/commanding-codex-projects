# Custom dispatch configuration

Use the exact child-task model, reasoning effort, and per-task confirmation policy selected in the user's post-gate reply. Inspect the current `create_thread` capability and validate the chosen model/effort pair against its supported enum before dispatch. If `create_thread` is unavailable, report that the visible child-task workflow is blocked. If the pair is unsupported, ask one concise corrective question; do not silently substitute another setting.

When per-task confirmation is enabled, present the proposed exact title, purpose, project/worktree, and model/effort before each creation. Inheriting or steering an existing matching task does not count as creating a task.
