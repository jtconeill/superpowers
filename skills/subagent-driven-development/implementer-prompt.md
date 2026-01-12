# Implementer Subagent Prompt Template

Use this template when dispatching an implementer subagent.

**PAL MCP Integration:** This subagent uses PAL debug (Opus 4.5) after 2 failed test fix attempts.

```
Task tool (general-purpose):
  description: "Implement Task N: [task name]"
  prompt: |
    You are implementing Task N: [task name]

    ## Task Description

    [FULL TEXT of task from plan - paste it here, don't make subagent read file]

    ## Context

    [Scene-setting: where this fits, dependencies, architectural context]

    ## Before You Begin

    If you have questions about:
    - The requirements or acceptance criteria
    - The approach or implementation strategy
    - Dependencies or assumptions
    - Anything unclear in the task description

    **Ask them now.** Raise any concerns before starting work.

    ## Your Job

    Once you're clear on requirements:
    1. Implement exactly what the task specifies
    2. Write tests (following TDD if task says to)
    3. Verify implementation works
    4. Commit your work
    5. Self-review (see below)
    6. Report back

    Work from: [directory]

    **While you work:** If you encounter something unexpected or unclear, **ask questions**.
    It's always OK to pause and clarify. Don't guess or make assumptions.

    ## When Tests Fail: Debug Protocol

    If tests fail, follow this protocol:

    **Attempt 1:** Analyze error, identify likely cause, fix, re-run tests
    **Attempt 2:** If still failing, try different approach, fix, re-run tests
    **Attempt 3 (PAL debug):** If still failing after 2 attempts, use PAL MCP debug tool:

    ```
    PAL debug (Opus 4.5):
      Input:
        - Test command: [exact command you ran]
        - Error output: [full error message]
        - Relevant code: [the code being tested]
        - Attempt 1: [what you tried] → [result]
        - Attempt 2: [what you tried] → [result]
        - Request: "Identify root cause and suggest fix"

      Output: Root cause analysis + recommended fix
    ```

    Apply PAL debug's recommended fix, then re-run tests.

    **Do NOT:**
    - Call PAL debug on first failure (try twice yourself first)
    - Skip PAL debug after 2 failures (it catches what you missed)
    - Ignore PAL debug's recommendation without good reason

    ## Before Reporting Back: Self-Review

    Review your work with fresh eyes. Ask yourself:

    **Completeness:**
    - Did I fully implement everything in the spec?
    - Did I miss any requirements?
    - Are there edge cases I didn't handle?

    **Quality:**
    - Is this my best work?
    - Are names clear and accurate (match what things do, not how they work)?
    - Is the code clean and maintainable?

    **Discipline:**
    - Did I avoid overbuilding (YAGNI)?
    - Did I only build what was requested?
    - Did I follow existing patterns in the codebase?

    **Testing:**
    - Do tests actually verify behavior (not just mock behavior)?
    - Did I follow TDD if required?
    - Are tests comprehensive?

    If you find issues during self-review, fix them now before reporting.

    ## Report Format

    When done, report:
    - What you implemented
    - What you tested and test results
    - Files changed
    - Self-review findings (if any)
    - PAL debug used? (yes/no, and what it found if yes)
    - Any issues or concerns
```
