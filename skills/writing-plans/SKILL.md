---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## PAL MCP Integration (Multi-Model Planning)

For better plans, use PAL MCP to get a second perspective:

### Step 1: Create Your Initial Plan

Write your plan following the structure below.

### Step 2: Get Gemini's Perspective

Use PAL clink to get Gemini's alternative plan:

```
PAL clink (Gemini 2.5 Flash):
  Input:
    - Design document: [path to design.md]
    - Your plan: [your initial plan]
    - Request: "Review this implementation plan. Create your own alternative
      plan if you see a better approach, or suggest improvements to this plan."

  Output: Alternative plan OR improvements to your plan
```

### Step 3: Compare Plans (If Significantly Different)

If Gemini's plan significantly differs from yours:

```
PAL thinkdeep (Opus 4.5):
  Input:
    - Plan A: [your plan]
    - Plan B: [Gemini's plan]
    - Question: "Compare these plans. What are the trade-offs?
      Which approach is better for a financial services app
      prioritizing security and maintainability?"

  Output: Analysis of trade-offs + recommendation
```

### Step 4: Merge with Consensus

Use PAL consensus to finalize:

```
PAL consensus (Gemini 2.5 Flash + GPT-4o + Sonnet 4.5):
  Input:
    - Plan A: [your plan]
    - Plan B: [Gemini's plan / improvements]
    - thinkdeep analysis: [if used]
    - Question: "What should the final plan include? Identify
      best ideas from each approach."

  Output: Consensus on final plan structure
```

### When Plans "Significantly Differ"

Plans significantly differ when:
- Different architecture approach (e.g., one service vs multiple)
- Different technology choices
- 50%+ difference in number of tasks
- One addresses concerns the other ignores
- Different ordering that affects dependencies

If plans mostly agree, skip thinkdeep and use consensus just to confirm.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

```markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

## Remember
- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits

## Execution Handoff

After saving the plan, offer execution choice:

**"Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (this session)** - I dispatch fresh subagent per task, review between tasks, fast iteration

**2. Parallel Session (separate)** - Open new session with executing-plans, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Stay in this session
- Fresh subagent per task + code review

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- **REQUIRED SUB-SKILL:** New session uses superpowers:executing-plans
