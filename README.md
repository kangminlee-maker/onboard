# claudecode-settings

Claude Code project onboarding command. Sets up `CLAUDE.md` and `.claude/rules/` for any repository.

## What it does

`/onboard` generates three files in your project:

```
<project>/
├── CLAUDE.md                              # Project instructions
└── .claude/
    └── rules/
        ├── coding-conventions.md          # Coding rules (language-adapted)
        └── project-patterns.md            # Naming, terminology, abbreviations
```

**Initial setup**: Scans the codebase, asks you about project context, generates all files.

**Update**: Re-scans the codebase and refreshes auto-detected sections (marked with `<!-- auto:* -->` comments) while preserving everything you wrote manually.

## Auto-detected vs user-provided

| Auto-detected (updated on re-run) | User-provided (preserved on re-run) |
|------------------------------------|--------------------------------------|
| Programming language, framework | Project overview |
| Package manager | Terminology definitions |
| Verification commands | Abbreviation registry |
| File naming conventions | Custom prohibitions |
| Default branch | |

## Install

Copy `onboard.md` to your global Claude Code commands directory:

```bash
cp onboard.md ~/.claude/commands/onboard.md
```

Then run `/onboard` in any project.

## Usage

```
# First time — creates all settings files
/onboard

# After codebase changes — updates auto-detected sections
/onboard
```
