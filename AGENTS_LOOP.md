# Night Shift Agent Loop

Load this file at the start of a night run and follow it literally. The goal is autonomous, high-discipline execution with minimal human cleanup the next day.

## Non-Negotiables

- Treat the current branch as the working branch unless explicitly told otherwise. This is usually `main`.
- Work bugs before specs.
- Only pick tasks from `./bugs` and `./specs`.
- Ignore any file whose basename starts with `draft-`.
- Create a fresh task branch for every selected bug or spec.
- Make regular, detailed commits during the task. Do not squash them away.
- Merge the finished task branch back into the working branch with preserved history. Prefer fast-forward merge.
- Record task start and finish times in `./report` using **AEST** timestamps (`UTC+10`), not vague relative times.
- If requirements are missing or conflicting, do not invent scope. Log the issue, preserve the work, and move on or stop.

## Inputs

- `./bugs`: bug write-ups and fixes to prioritize first
- `./specs`: feature specs to implement after bugs are clear
- `REVIEW_PERSONAS.md`: the six critical reviewers
- `AGENTS.md`: routing doc for deeper project guidance
- Repo docs and code: the source of truth for implementation details

## Daily Report

- Ensure `./report` exists before starting task work.
- Use one report file per AEST day: `./report/{project-name}-YYYY-MM-DD.md`.
- Add one section per task with:
  - task type: `bug` or `spec`
  - task file
  - branch name
  - start time in AEST
  - finish time in AEST
  - status: merged, blocked, skipped, or abandoned
  - commits created
  - short note on validations run
  - short note on blockers or follow-ups

## The Loop

1. **Prep**
   - Inspect the current branch and working tree.
   - Protect any unrelated uncommitted work without losing it. Prefer a safe commit or clearly named stash if needed.
   - Ensure the working branch is in a usable state before starting new work.
   - Run the current baseline validation suite and fix any pre-existing failures before picking up new work.
   - Create today's `./report/YYYY-MM-DD.md` file if it does not exist.

2. **Pick the next task**
   - Look in `./bugs` first.
   - If no actionable bug exists, look in `./specs`.
   - Ignore `draft-*` files in both directories.
   - Pick the smallest clear, actionable item.
   - If a task is unclear or blocked, record that in the report and skip it rather than guessing.

3. **Create the task branch**
   - Branch from the working branch.
   - Use a descriptive branch name:
     - `bug/<short-slug>`
     - `spec/<short-slug>`
   - Record the task start time in the daily report using AEST.
   - Record the task file and branch name before implementation begins.

4. **Load and analyze the task**
   - Read the selected bug or spec closely.
   - Extract constraints, acceptance criteria, edge cases, and explicit non-goals.
   - Do not start coding until the task is coherent.

5. **Load relevant docs and code**
   - Read only the docs needed for this task.
   - Find the relevant code paths, tests, and interfaces.
   - Build a concrete understanding of how this part of the system currently works.

6. **Develop the testing plan**
   - Define the tests that prove the task is complete and safe.
   - Cover happy paths, edge cases, regressions, and failure modes.
   - Prefer the highest-signal tests available.

7. **Write tests first**
   - Add or update tests before implementation.
   - Run them and expect failures.
   - Use the failures to prove the tests are meaningful.

8. **Develop the internal implementation plan**
   - Create your own working plan for the task.
   - Break the work into small, verifiable steps.
   - The human does not need to read this plan, but the plan must be real.

9. **Run the review personas on the plan**
   - Use all six personas from `REVIEW_PERSONAS.md`.
   - Ask them to review the plan against the task, docs, tests, and likely failure modes.
   - Require concrete objections, not vague commentary.

10. **Adapt the plan until the personas are green**
    - Revise the plan based on reviewer feedback.
    - Resolve disagreements before implementation.
    - If the reviewers surface doc gaps, queue doc fixes as part of the task.

11. **Implement the task**
    - Make the smallest coherent change that moves the task forward.
    - Keep docs, tests, and code aligned.
    - Commit at meaningful milestones with detailed commit messages that explain:
      - what changed
      - why it changed
      - what was validated

12. **Run strict validations**
    - Run the most strict checks available for the task:
      - targeted tests
      - type checking
      - linting
      - build or compile checks
      - static analysis
      - any repo-specific validation tools
    - Fix failures before moving on.

13. **Run the full suite**
    - Run the full test suite after the task appears complete.
    - Treat regressions as part of the current task.
    - Do not merge work that only passes targeted checks.

14. **Run the review personas on the implementation diff**
    - Review the actual diff, not just the plan.
    - Loop between implementation and review until all personas are green.
    - If a persona finds a documentation gap, fix the docs in the same task when reasonable.

15. **Capture unrelated follow-ups**
    - If you discover unrelated issues, do not silently fix them inside the current task.
    - Add them as new draft bug notes in `./bugs`, using the `draft-` prefix for human review.
    - Mention them briefly in the daily report.

16. **Wrap up the task**
    - Update any relevant docs or changelog entries.
    - Ensure the branch history is reviewable and complete.
    - Merge the task branch back into the working branch without squashing commits. Prefer fast-forward merge.
    - If merge fails cleanly, stop and record the blocker rather than improvising a risky history rewrite.
    - Record the finish time in AEST and list the commits in the daily report.

17. **Loop to the next task**
    - Return to step 1 and select the next actionable bug or spec.
    - Continue until no clear non-draft task remains or a hard blocker requires human input.
    - When the run is complete, add a short end-of-run summary to the daily report.

18. **Go silent**
    - Stop when the queue is exhausted or blocked.
    - Leave a concise recap for the human in the report.
    - Details belong in commits and docs, not in a long essay.

## Commit Standard

Use detailed commit messages meant for human review the next morning.

Preferred shape:

```text
<type>: <specific summary>

Context:
- why this task exists

Changes:
- the main code and doc changes

Validation:
- exact checks run
```

## Stop Conditions

Stop and leave a clear note in the report if:

- the task spec or bug report is too incomplete to implement safely
- the working tree cannot be protected safely
- baseline validations are already broken and cannot be repaired in reasonable time
- merge-back to the working branch would require risky manual conflict resolution
