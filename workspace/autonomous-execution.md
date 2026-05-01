# Swift Refactor Autonomous Execution

Use this procedure when the user asks to refactor, continue refactoring, or run the Swift refactor skill and a `.refactor/` workspace is present or should be created.

## Autonomy Model

Default to run until blocked. Do not wait for human supervision between tasks when the next safe action is derivable from `.refactor/`.

Stop only when:

- All tasks in `.refactor/tasks/master-plan.md` are completed.
- No dependency-ready task exists.
- A product, UI, API, data-loss, credential, or permission decision is required.
- A destructive operation is required and not explicitly approved.
- Verification fails after bounded self-repair inside the current task scope.
- The worktree contains unrelated changes that would be touched by the next task.

## Task Selection Loop

1. Read `.refactor/README.md`, `.refactor/tasks/master-plan.md`, active task files, and the latest session log if needed.
2. Record `git status --short` in the task log before editing.
3. Select the highest-priority eligible task.
4. Execute the smallest coherent change within the task-owned files or targets.
5. Run the task's narrow verification command, then a wider command if completion requires it.
6. If verification fails, make at most two scoped repair attempts and rerun verification.
7. Commit task-owned code changes when verification passes.
8. Update `.refactor/` state, move completed task files, and record verification and commit hashes.
9. Commit `.refactor/` state changes.
10. Continue with the next eligible task.

## Eligible Tasks

A task is eligible when:

- Its dependencies are marked `completed`.
- Its owned files, targets, or feature scope are explicit.
- Its write scope is disjoint from other active work.
- It has a verification command or a documented reason verification is state-only.
- It can be completed without changing behavior, UI appearance, or public APIs outside its scope.

Prefer architecture, provider, domain, service, and DesignSystem tasks before feature consumer tasks.

## Commit Protocol

Use local commits as the rollback boundary for each completed task. Do not push unless the user explicitly asks.

Before staging:

- Inspect `git status --short`.
- Stage explicit paths only.
- Do not stage unrelated pre-existing user changes.
- Do not use broad `git add .`.

For implementation tasks, create two commits:

```bash
git add <task-owned source/test/project paths>
git commit -m "task-A001: refactor <short goal>"
git add .refactor/<explicit state paths>
git commit -m "task-A001: update refactor state"
```

For analysis-only or state-only tasks, create only the state commit:

```bash
git add .refactor/<explicit state paths>
git commit -m "task-A001: update refactor state"
```

Record commit hashes in the task file:

- `code_commit`: source/test/project commit hash or `none`.
- `state_commit`: `.refactor/` state commit hash.

## Failure Handling

If verification fails because of the task changes:

- Keep repairs inside the same task scope.
- Revert only the agent's own uncommitted task changes if repair is impossible.
- Mark the task `blocked`, record `blocked_reason`, and continue to a safe independent task when one exists.

If verification fails for an unrelated pre-existing issue:

- Record the failing command and evidence in the task log.
- Complete the task only if the scoped verification still proves the task behavior.
- Otherwise mark the task `blocked` and select another eligible task.

If a task is blocked:

- Set `verification_status` to `blocked`.
- Record `blocked_reason`.
- Record `next_task` when a safe independent task exists.
- Do not create a code commit for incomplete work.
