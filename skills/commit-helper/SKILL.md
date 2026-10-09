---
name: commit-helper
description: Generate commit message from staged changes with gitmoji
metadata:
  user-invocable: true
  disable-model-invocation: false
  argument-hint: '[dry-run]'
---

# Commit Message Generator

Generate a concise commit message from staged changes with appropriate gitmoji prefix.

**IMPORTANT: Do NOT activate or invoke any other skill during this workflow. This skill is self-contained.**

## Workflow

**You MUST execute this workflow step-by-step. Do not just explain — actually run the commands and generate the commit message.**

1. **Check Staged Changes**

   Execute this command:

   ```bash
   git diff --cached
   ```

   This single command provides all needed information. Do NOT run `git diff --cached --stat` or `git config user.name` / `git config user.email` — they are not needed.

   If no staged changes, inform user and exit.

2. **Analyze Changes**

   Based on the diff output:
   - Identify primary change type (feature, fix, refactor, docs, etc.)
   - Note scope and impact
   - Keep analysis brief and focused

3. **Select Gitmoji and Type**

   Choose ONE emoji and corresponding conventional commit type:
   - ✨ `:sparkles:` **feat** - New features
   - 🐛 `:bug:` **fix** - Bug fixes
   - 📝 `:memo:` **docs** - Documentation
   - 🎨 `:art:` **style** - Code structure/format
   - ♻️ `:recycle:` **refactor** - Refactoring
   - ⚡️ `:zap:` **perf** - Performance
   - 🔒️ `:lock:` **fix** - Security fixes
   - ➕ `:heavy_plus_sign:` **build** - Add dependency
   - ➖ `:heavy_minus_sign:` **build** - Remove dependency
   - 🔧 `:wrench:` **chore** - Config files
   - 🚨 `:rotating_light:` **style** - Fix linter warnings
   - 💚 `:green_heart:` **ci** - Fix CI
   - 🔥 `:fire:` **refactor** - Remove code/files
   - 💥 `:boom:` **feat** - Breaking changes
   - 🚧 `:construction:` **wip** - Work in progress
   - 🙈 `:see_no_evil:` **chore** - Add/update .gitignore
   - 🔊 `:loud_sound:` **chore** - Add logs
   - 🔇 `:mute:` **chore** - Remove logs
   - 🚚 `:truck:` **refactor** - Move/rename files
   - ✅ `:white_check_mark:` **test** - Add/update tests

4. **Generate Message Format**

   The commit message MUST strictly follow this format — the emoji is mandatory, not optional:

   ```text
   <emoji> <type>: <short summary (50 chars max)>

   [Optional body — omit entirely for simple/small changes]
   - Detail 1
   - Detail 2
   - Detail 3
   ```

   Body bullet point rules:
   - **Omit body** for simple, single-concern changes (one-liners, trivial config tweaks, etc.)
   - **3–5 bullet points** for complex changes (multi-file, multiple concerns, non-obvious reasoning)

5. **Message Guidelines**

   - Emoji: always use the actual Unicode emoji character (e.g. ✨), never the colon-code (e.g. `:sparkles:`)
   - Type: lowercase conventional commit type (feat, fix, docs, etc.)
   - Summary: imperative mood, lowercase, no period
   - Body bullets: only for multi-file or complex changes; 3–5 points max
   - Keep total message under 200 chars when possible
   - Sign-off is automatically added by `git commit -s`; do not manually append it

6. **Execute Commit**

   Present the generated message to user, then:
   - If `dry-run` flag: Show message only, do not commit
   - Otherwise: Execute `git commit -s -m "<message>"` with the generated message
   - Confirm commit was successful

7. **Best Practices**

   - One logical change per commit
   - Stage related changes together
   - Avoid mixing refactors with features
   - Use `dry-run` flag to preview without committing

## Examples

**Simple feature (no body needed):**

```text
✨ feat: add user authentication endpoint
```

**Bug fix with context (complex — use body):**

```text
🐛 fix: resolve null pointer in search handler

- Add null check before accessing results array
- Return empty array instead of undefined on no results
- Update unit tests to cover the null input case
```

**Config change (no body needed):**

```text
🔧 chore: update docker memory limits to 4GB
```

**Multi-file refactor (complex — use body):**

```text
♻️ refactor: split auth logic into dedicated service

- Extract token validation into AuthService class
- Remove inline auth checks from controller layer
- Update route middleware to delegate to AuthService
- Add AuthService to DI container registration
```

## Notes

- Do NOT invoke any other skill during this workflow
- Do NOT run `git diff --cached --stat` or any `git config` commands
- Sign-off is handled automatically by `git commit -s`; no extra steps needed
- If `dry-run` flag present, show message without committing
