# The "Night Shift" Agentic Workflow

Previous attempts at agentic workflows left him exhausted, overwhelmed, and feeling out of touch with the systems he was building. They also degraded quality too much.

His current workflow is about 5x faster, produces better quality, improves system understanding, and makes the work fun again. He calls this the **Night Shift** workflow.

## Core Ideas

The key ideas behind Night Shift are:

- Human time, energy, and attention are highly constrained and expensive.
- Agent time and tokens are cheap, plentiful, and practically unconstrained.
- The human should remain in control, using the minimum effort required, but no less.
- Agents should work without needing constant babysitting.
- The workflow should avoid endless prompting and re-prompting.
- Agents should improve over time.
- Focus should stay on one thing at a time.
- The human should stay in control without feeling anxious about idle agents waiting for input.

The framing is simple: the human takes the **day shift**, and the AI agents take the **night shift**. During the day, the human prepares everything as thoroughly as possible. During the night, the agents work autonomously while the human rests, with results ready by the next morning.

## The Day Shift

During the day shift, the human works alone while the agents sit idle.

This is the time for:

- Talking with other humans
- Gathering requirements
- Thinking through architecture
- Writing detailed specification documents

The emphasis is on deep thinking and sustainable pacing. AI autocomplete is snoozed. The goal is to do as much of the design work personally as needed so that the system, the tradeoffs, and the likely edge cases are fully understood before handing anything off.

Specs should describe:

- The feature itself
- Edge cases
- Possible problems
- Likely solutions

These specs are organized primarily for the human author, not for the agent. The act of writing them sharpens understanding.

Spec writing is hard at first, but it gets easier with practice.

When a spec is finished, the implementation agent is **not** started immediately. A break comes first. If there is still time in the day, another feature can be specified in the same deliberate way.

The pace is sustainable. Breaks are normal. The agents remain idle and silent.

### Limited AI Use During the Day

AI is still used during the day shift, but only in two narrow ways:

1. **Info lookup**  
   Short "Ask" mode queries bring back concise information. The AI acts like a sharp research assistant, not a design partner.

2. **Plan review**  
   Once a spec is written, the agent reviews it and returns a concise checklist of edge cases or gaps. The response should be brief, not essay-length. The human then manually folds those items into the spec and evaluates them independently.

### End of Day Handoff

At the end of the day:

- Unfinished specs can wait until tomorrow.
- Completed spec docs live in `./Specs`.
- Files named `draft-*` are ignored by the agent.

Then the chosen coding agent is launched, such as Claude Code, Cursor, or Codex.

The agent is told to load `@AGENTS_LOOP.md`, which explains the night workflow.

`@AGENTS.md` serves as a small router, roughly 150 lines long, telling the agent where to find:

- Workflow docs
- Skill docs
- System documentation

This matters because specs do not need to solve everything. The better the documentation and routing, the smaller and more focused the specs can be.

Once that handoff is complete, the computer is locked for the evening and the Night Shift takes over.

## The Night Shift Gets to Work

While the human is away, the agent works through a repeatable loop:

1. **Prep**  
   Clean the working tree by analyzing uncommitted work and doing the right thing with it, such as stashing or committing. Run the current test suite and fix any existing failures.

2. **Pick the next task**  
   Choose from bugs first. If bugs are complete, choose a feature with a completed spec.

3. **Load and analyze the spec**

4. **Load relevant docs and code**

5. **Develop a testing plan**  
   This is considered absolutely critical.

6. **Write extensive tests first**  
   Run them and expect failures.

7. **Develop an internal implementation plan**  
   The human never needs to read this plan.

8. **Run review agents based on six personas**  
   These personas are defined in `REVIEW_PERSONAS.md`:
   - Designer
   - Architect
   - Domain Expert
   - Code Expert
   - Performance Expert
   - Human Advocate

9. **Adapt the plan based on reviewer feedback**  
   Loop back through planning until all review agents give a green light.

10. **Implement the work**  
    This includes code changes and any needed documentation updates in `Docs`.

11. **Run strict validations**  
    Run type checking, linting, compiler checks, static analysis, bundle-size reporting, and all relevant tests. Iterate until everything is passing.

12. **Run the entire test suite again**  
    Protect against regressions and fix any newly introduced issues.

13. **Run the review agents again on the implementation diff**  
    Loop back to implementation until all reviewers approve.

14. **Capture unrelated follow-ups**  
    Add unrelated issues noticed along the way to the TODO doc for later human review.

15. **Wrap up the work**  
    Write a changelog entry and create a detailed commit message with strong human-review context.

16. **Loop to the next task**

17. **Write a final human report when everything is done**  
    This report should be extremely concise. The details should live in the commit messages.

18. **Go silent and wait**

Automated testing is essential. This workflow does not work without:

- A robust end-to-end testing harness
- Excellent documentation
- Enough system context for agents to create and validate their own tests

## The Day Shift Clocks In

When the human returns, the workday begins with review:

- Check the changelog and agent recap.
- Review the work commit by commit.
- Read each commit message.
- Review implementation diffs, tests, and documentation changes.

### Stacked Commits

All commits stay on the same branch so they stack on top of each other.

Benefits include:

- Improvements carrying forward into later commits
- Fewer conflicts
- Better cumulative results
- Less duplicated work

### Quick Fixes

If something needs a quick correction, it can be fixed by hand or in an interactive agent session. But before changing the code, the docs, workflow, and validations should be analyzed and corrected first.

### Postmortems

If the agent misbehaves, the response is not simply to patch the code.

Instead:

- Investigate why the agent made the wrong decision.
- Analyze which docs, skills, or workflow guidance led it astray.
- Improve that context first.
- Only then fix the original issue.

The point is to amortize the improvement over the rest of the project so that the system keeps getting better.

### Manual Testing

Almost every change is tested manually and thoroughly, not just to catch bugs, but to reveal:

- Gaps in documentation
- Gaps in skills
- Gaps in specs
- Gaps in validations and tests
- Gaps in personal understanding of the system

After that, the cycle returns to requirements gathering, architecture, and spec writing.

The Night Shift needs the human's best work, so the human protects that work by avoiding context switching and agent babysitting.

## The Feedback Loop

An important characteristic of this workflow is that the human is **not** there to babysit the agents.

That means imperfections in docs or workflow cannot be patched over through real-time steering. The system itself has to improve continuously so that each morning does not begin with cleanup.

The goal is to spend whatever tokens are necessary to get the agent's output as close to correct as possible before a human ever reviews it.

A human reviewer should not be catching obvious, basic issues. If that is happening, then the automated validations are not strong enough and need to be improved. That includes agent review steps as well.

The standard is high: human time and energy are precious, so the workflow must demand the agent's best effort.

## Results So Far

This workflow began in early form about a month before the article was written, and the reported results improved day by day.

The outcomes so far:

- Less babysitting
- More time spent thinking about real problems
- Higher productivity
- Less context switching
- A calmer, more peaceful workflow
- Better uninterrupted overnight execution

One experiment involved having Codex observe what Claude was doing and write feedback into a file. Claude knew this was happening, tailed the file, and pulled the feedback into its process. According to the write-up, it worked surprisingly well and may be used more often in the future.

## Example Reviewer Prompt

```text
I have another agent doing the AGENTS_LOOP.md right now, working through TODOS.md. What I'd like you to do is do your own loop as an expert reviewer.

    Sleep for 5 minutes at a time.
    Then, wake up look at the current git log to see if any new commits have landed
    Systematically review them against the corresponding TODOS.md entry.
    Provide your feedback about each commit in a file named TODOS_CODEX_REVIEW.md located in the same folder as TODOS.md.
    I will tell the other agent to take a look at your notes for it in that folder, and it will then incorporate your feedback in a separate, second loop.
    If you do not find any new commits (even if there are working tree changes), please don't do anything and just sleep again.
    You'll know you should be done when all the current TODOs are marked complete or moved into the 'NEEDS INPUT FROM USER' section. In that case, you can stop.
    If you do not see any changes within 30 minutes, go ahead and stop, as the other agent may have quit prematurely.
```
