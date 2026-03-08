---
name: fix-bug-pro
description: |
  Comprehensive bug investigation, fix, and regression-proofing workflow.
  Use this skill when the user reports a bug, asks to fix an error, mentions
  failing tests, stack traces, unexpected behavior, or wants to debug an issue.
  Also triggers on: "почини баг", "исправь ошибку", "найди причину", "debug",
  "failing test", "not working", "crashes when", "throws error".
---

# Bug Fixing Workflow (Pro Edition)

When this skill is invoked, **do not start changing code immediately**.

## Phase 1: Information Gathering

First, ask the user for bug details in this structure:

```
Какую именно ошибку нужно исправить?

Пришлите по возможности:
1. Краткое описание проблемы
2. Текст ошибки / stack trace / failing test / логи
3. Ожидаемое поведение
4. Фактическое поведение
5. Где это проявляется: endpoint / service / job / CLI / UI / test
6. Как это воспроизвести (шаги)
7. Связанный файл, модуль или пакет, если уже известен
8. Когда впервые появилось (недавние изменения, деплой)
```

If information is incomplete, ask **short targeted follow-up questions**.
Prioritize: stack traces > failing tests > logs > reproduction steps.

---

## Phase 2: Bug Categorization

After gathering initial info, **categorize the bug** to guide your approach:

| Category | Indicators | Key Investigation Tools |
|----------|-----------|------------------------|
| **Logic Error** | Wrong output, unexpected flow | Grep for related functions, trace execution |
| **Null/Undefined** | NPE, undefined is not a function | Grep for null checks, find initialization |
| **Type Mismatch** | Type errors, coercion issues | Check types, interfaces, contracts |
| **Race Condition** | Intermittent, timing-related | Look for shared state, async patterns |
| **Memory Issue** | Leaks, OOM, growing usage | Check resource cleanup, listeners, caches |
| **Integration** | API failures, external services | Check contracts, timeouts, error handling |
| **Performance** | Slow, timeout, high CPU/memory | Profile, check algorithms, N+1 queries |
| **Security** | Injection, auth bypass, data leak | Check input validation, sanitization |
| **Configuration** | Works locally, fails in env | Check env vars, configs, secrets |

State your category hypothesis explicitly.

---

## Phase 3: Investigation Workflow

### 3.1 Code Pattern Search (Use Grep/Glob)

Before diving into code, search for patterns:

```
# Find related code
Grep for: error message text, function names, class names
Grep for: variable names from stack trace
Glob for: files matching the module/component pattern

# Find similar patterns (potential duplicate bugs)
Grep for: same pattern in other files
```

### 3.2 Git Analysis

Use git to understand context:

```bash
# Recent changes to affected files
git log --oneline -20 -- <file>
git blame <file>  # Who wrote this, when, in what context

# Find when bug was introduced (if regression)
git log -p -- <file> | head -500  # Recent changes with diff
```

### 3.3 Reproduce or Infer

**Best case**: Reproduce the bug
- Run failing tests: `npm test`, `pytest`, `go test`, etc.
- Try the reproduction steps the user provided
- Create a minimal reproduction case

**If can't reproduce**:
- Infer from code analysis
- Check logs for patterns
- Ask user for more details

### 3.4 Root Cause Analysis

Dig until you find the **true root cause**, not just a symptom:

1. Start from error location (stack trace line)
2. Trace backward: how did we get here?
3. Check: what assumptions does this code make?
4. Verify: are those assumptions always true?
5. Find: the specific condition that breaks the assumption

**Document your reasoning** - this helps reviewers understand the fix.

---

## Phase 4: Fix Implementation

### 4.1 Minimal Safe Fix

- Make the **smallest change** that fixes the root cause
- Do NOT do unrelated refactoring
- Do NOT change public interfaces unless absolutely necessary
- If multiple fix options exist, choose the **safest** one

### 4.2 Consider Side Effects

Before finalizing, assess:

| Area | Questions to Ask |
|------|-----------------|
| **Regression** | Could this break existing functionality? |
| **Performance** | Does this add overhead? Is it acceptable? |
| **Security** | Does this introduce or fix a security issue? |
| **Concurrency** | Are there thread-safety implications? |
| **Data** | Could this corrupt or lose data? |
| **API** | Does this change external contracts? |
| **Dependencies** | Does this require package changes? |

---

## Phase 5: Test Coverage

### 5.1 Why Tests Missed It

Explain why existing tests didn't catch this bug:
- Missing test case entirely?
- Wrong test assumptions?
- Mock hiding the real issue?
- Edge case not covered?

### 5.2 Add Regression Tests

Create tests that:
- [ ] **Fail before the fix** - prove the bug exists
- [ ] **Pass after the fix** - prove the fix works
- [ ] **Cover edge cases** - prevent similar bugs
- [ ] **Are minimal** - test the specific bug, not everything

For different bug types, add specific tests:

| Bug Type | Test Approach |
|----------|--------------|
| Logic | Unit tests for edge cases |
| Race Condition | Concurrent tests, flake detection |
| Integration | Contract tests, mock external deps |
| Performance | Benchmarks, load tests |
| Security | Penetration tests, input fuzzing |

---

## Phase 6: Validation Checklist

Run through this checklist before declaring done:

### Code Quality
- [ ] Fix addresses root cause, not symptom
- [ ] No unrelated changes included
- [ ] Code follows project style conventions
- [ ] No hardcoded values that should be configurable
- [ ] Error messages are helpful

### Testing
- [ ] All existing tests pass
- [ ] New regression test(s) added
- [ ] Edge cases covered
- [ ] Manual reproduction verified (if applicable)

### Impact Assessment
- [ ] No breaking changes to public APIs
- [ ] No security vulnerabilities introduced
- [ ] Performance impact is acceptable
- [ ] No new dependencies without approval

### Documentation
- [ ] Code comments updated if logic is non-obvious
- [ ] API docs updated if behavior changed
- [ ] README/CHANGELOG updated if user-facing
- [ ] Error messages are user-friendly

---

## Phase 7: Rollback Plan

Prepare for worst-case scenarios:

1. **What could go wrong?** - List potential issues
2. **How to detect?** - Monitoring, alerts, user reports
3. **How to rollback?** - Revert commit, feature flag, hotfix
4. **Data migration?** - If DB changes, is it reversible?

If the fix is risky:
- Consider a feature flag
- Deploy to staging first
- Have monitoring alerts ready
- Document rollback steps

---

## Output Format

Return findings in this structure:

```
## 1. Bug Summary
- Category: [from categorization table]
- Severity: [Critical/High/Medium/Low]
- Affected component: [module/service/endpoint]

## 2. Reproduction
- Steps to reproduce
- Observed behavior
- Expected behavior

## 3. Root Cause
- Primary cause: [what's actually wrong]
- Contributing factors: [what made it possible]
- Code location: [file:line]

## 4. Why Tests Missed It
- [explanation]

## 5. Fix Implemented
- Change summary: [what was changed]
- Files modified: [list]
- Diff size: [lines changed]

## 6. Test Updates
- New tests added: [describe]
- Existing tests modified: [if any]

## 7. Impact Assessment
- Regression risk: [Low/Medium/High] - [reason]
- Performance impact: [None/Minor/Major] - [details]
- Security implications: [None/Concern] - [details]

## 8. Validation Steps
- [ ] All tests pass
- [ ] Manual verification done
- [ ] Edge cases covered

## 9. Rollback Plan
- Risk level: [Low/Medium/High]
- Rollback method: [git revert / feature flag / hotfix]
- Monitoring: [what to watch]

## 10. Follow-up Recommendations
- [optional improvements, tech debt, related issues]
```

---

## Quick Reference: Tools to Use

| Task | Tool |
|------|------|
| Find files | Glob |
| Search code | Grep |
| Read code | Read |
| Edit code | Edit |
| Run tests | Bash (npm test, pytest, etc.) |
| Git history | Bash (git log, git blame) |
| Debug | Bash (node inspect, pdb, delve) |

---

## Remember

- **Minimal diffs** - smallest safe change
- **Root cause** - not just symptoms
- **Regression tests** - prevent recurrence
- **Document reasoning** - help reviewers
- **Validate thoroughly** - use the checklist
- **Plan for failure** - have a rollback plan