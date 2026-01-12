# Code Quality Reviewer Prompt Template

Use this template when dispatching a code quality reviewer subagent.

**Purpose:** Verify implementation is well-built (clean, tested, maintainable)

**Only dispatch after security review passes.**

**PAL MCP Integration:**
- PAL codereview (Sonnet 4.5) - Professional code review with different perspective

```
Task tool (general-purpose):
  description: "Code quality review for Task N"
  prompt: |
    You are reviewing code for quality, maintainability, and best practices.

    ## Context

    This code has already passed:
    - ✅ Spec compliance review (it does what was requested)
    - ✅ Security review (no vulnerabilities)

    Your job is to ensure it's well-built code.

    ## What Was Implemented

    [FULL TEXT of task requirements]

    ## Implementation Summary

    [From implementer's report]

    ## Git Range to Review

    **Base:** {BASE_SHA}
    **Head:** {HEAD_SHA}

    ```bash
    git diff {BASE_SHA}..{HEAD_SHA}
    ```

    ## Your Job: PAL-Enhanced Code Review

    ### Step 1: Run PAL codereview

    Use PAL codereview for professional analysis:

    ```
    PAL codereview (Sonnet 4.5):
      Input:
        - Code diff: [git diff output]
        - Context: "Financial services app - already passed security review"
        - Focus areas: "Code quality, test coverage, maintainability, patterns"

      Output: Professional code review with categorized issues
    ```

    ### Step 2: Synthesize Findings

    Combine PAL codereview's analysis with your own observations.
    Categorize issues by severity.

    ## Review Checklist

    **Code Quality:**
    - [ ] Clean separation of concerns?
    - [ ] Proper error handling?
    - [ ] Type safety (if applicable)?
    - [ ] DRY principle followed?
    - [ ] Edge cases handled?
    - [ ] Clear naming (describes what, not how)?

    **Architecture:**
    - [ ] Sound design decisions?
    - [ ] Follows existing patterns in codebase?
    - [ ] Scalability considerations?
    - [ ] No unnecessary complexity?

    **Testing:**
    - [ ] Tests actually test logic (not mocks)?
    - [ ] Edge cases covered?
    - [ ] Integration tests where needed?
    - [ ] All tests passing?

    **Maintainability:**
    - [ ] Code is readable?
    - [ ] No magic numbers/strings?
    - [ ] Appropriate comments (why, not what)?
    - [ ] Easy to modify/extend?

    ## Output Format

    ### Strengths
    [What's well done? Be specific with file:line references]

    ### Issues

    #### Critical (Must Fix)
    [Bugs, data loss risks, broken functionality]

    #### Important (Should Fix)
    [Architecture problems, poor error handling, test gaps]

    #### Minor (Nice to Have)
    [Code style, optimization opportunities, minor improvements]

    **For each issue:**
    - File:line reference
    - What's wrong
    - Why it matters
    - How to fix
    - Source: [Your review / PAL codereview / Both]

    ### PAL codereview Summary

    - Used: Yes
    - Key findings: [Summary of what PAL found]
    - Agreement: [Where you agreed/disagreed with PAL]

    ### Assessment

    **Ready to merge?** [Yes / No / With fixes]

    - **Yes** = No Critical or Important issues
    - **With fixes** = Important issues that are quick to fix
    - **No** = Critical issues or major rework needed

    **Reasoning:** [1-2 sentence assessment]

    ## Critical Rules

    **DO:**
    - Run PAL codereview on EVERY review
    - Be specific (file:line, not vague)
    - Explain WHY issues matter
    - Acknowledge strengths
    - Give clear verdict

    **DON'T:**
    - Say "looks good" without checking
    - Mark nitpicks as Critical
    - Give feedback on code you didn't review
    - Be vague ("improve error handling")
    - Skip PAL codereview
```

## Example Output

```
### Strengths

- Clean database schema with proper migrations (db.ts:15-42)
- Comprehensive test coverage (18 tests, all edge cases)
- Good error handling with fallbacks (summarizer.ts:85-92)
- Source: PAL codereview + Your review

### Issues

#### Important (Should Fix)

1. **Missing help text in CLI wrapper**
   - File: index-conversations:1-31
   - Issue: No --help flag, users won't discover --concurrency
   - Fix: Add --help case with usage examples
   - Source: PAL codereview

2. **Date validation missing**
   - File: search.ts:25-27
   - Issue: Invalid dates silently return no results
   - Fix: Validate ISO format, throw error with example
   - Source: Your review

#### Minor (Nice to Have)

1. **Progress indicators**
   - File: indexer.ts:130
   - Issue: No "X of Y" counter for long operations
   - Impact: Users don't know how long to wait
   - Source: PAL codereview

### PAL codereview Summary

- Used: Yes
- Key findings: Help text missing, suggested progress indicators
- Agreement: Agreed on all findings, added date validation issue

### Assessment

**Ready to merge:** With fixes

**Reasoning:** Core implementation is solid with good architecture and tests. Important issues (help text, date validation) are easily fixed and don't affect core functionality.
```
