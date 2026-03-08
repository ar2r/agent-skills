# fix-bug-pro Plugin

Comprehensive bug investigation, fix, and regression-proofing workflow for professional bug fixing.

## Features

This plugin provides a structured 7-phase workflow for bug fixing:

1. **Information Gathering** - Collect bug details systematically
2. **Bug Categorization** - Classify by type (logic, race condition, memory, etc.)
3. **Investigation** - Search code, analyze git history, reproduce the issue
4. **Fix Implementation** - Minimal safe fixes with side-effect analysis
5. **Test Coverage** - Add regression tests that would have caught the bug
6. **Validation Checklist** - Code quality, testing, impact assessment
7. **Rollback Plan** - Prepare for worst-case scenarios

## Installation

### Option 1: Via Claude Code CLI (Recommended)

```bash
/plugins add https://github.com/ar2r/claude-skills
```

### Option 2: Manual Installation

Copy the plugin directory to your Claude plugins folder:

```bash
cp -r plugins/fix-bug-pro ~/.claude/plugins/
```

## Usage

The skill is automatically triggered when you:

- Report a bug: "There's a bug in my code"
- Ask to fix an error: "Fix this error"
- Mention failing tests: "The tests are failing"
- Describe unexpected behavior: "This crashes when I click X"
- Use trigger phrases: "почини баг", "исправь ошибку", "debug", "failing test", "not working"

### Example Triggers

```
Fix the null pointer exception in UserService
Debug why the payment is failing
This test is failing, can you fix it?
```

## Skill Workflow

When invoked, the skill follows a systematic approach:

1. Asks clarifying questions about the bug
2. Categorizes the bug type
3. Investigates root cause using code search and git history
4. Implements minimal safe fix
5. Suggests/adds regression tests
6. Provides impact assessment and rollback plan

## License

MIT