---
name: pr-helper
description: Create a pull request with smart title and description
metadata:
  user-invocable: true
  disable-model-invocation: false
  argument-hint: <pr-url>
---

You are creating a pull request. Follow this workflow:

**If PR URL is provided as argument**: Skip all steps 0–3 and go directly to Step 4 (Get Accurate PR Diff) using the provided PR URL. Then continue with Steps 5–7.

**If no PR URL provided**: Follow all steps below to create a new PR.

---

## Step 0: Identify Git Repository

Run this command to identify the git repository context:

```bash
[ -d ".git" ] && echo "Current: $(basename "$PWD")" || true; find . -maxdepth 2 -name ".git" -type d | sed 's|/\.git||' | sed 's|^\./||' | grep -v "^$" | sort
```

**Decision Logic**:

1. **If output shows "Current: [repo-name]"**: Current directory is a git repo → Proceed to Step 1
2. **If output lists multiple repos**: **MUST ask user** to select which repository to create PR for. Do NOT make assumptions. After user selects, `cd` into that directory.
3. **If no git repos found**: Error — cannot create PR without a git repository.

## Step 1: Gather All Git Context

Run this **single comprehensive command** to collect all necessary information:

```bash
echo "=== CURRENT BRANCH ===" && \
git branch --show-current && \
echo -e "\n=== LOCAL BRANCHES ===" && \
git branch && \
echo -e "\n=== REMOTE BRANCHES ===" && \
git branch -r && \
echo -e "\n=== REMOTES ===" && \
git remote -v
```

## Step 2: Determine Target Branch

**ALWAYS ask user for target branch** unless they have already explicitly specified it.

**CRITICAL FILTERING RULES**:

1. **EXCLUDE** the current branch from options.
2. **EXCLUDE** `origin/HEAD`.
3. **PREFER** common targets: `origin/main`, `origin/master`, `origin/develop`, `upstream/main`.
4. Always include "Other (specify)" option.

**NEVER assume the target branch is `main`. Always confirm if not provided.**

## Step 3: Create Draft PR with Placeholder

Create the PR immediately with a placeholder title to get the PR number and accurate diff.

### Option A: GitHub CLI (Preferred)

```bash
gh pr create \
  --title "Draft: PR from $(git branch --show-current)" \
  --body "Analyzing changes..." \
  --base <target-branch> \
  --head <origin-owner>:<current-branch> \
  --repo <target-owner>/<repo-name> \
  --draft
```

Always use `<origin-owner>:<branch>` for `--head` (extracted from `origin` remote URL) and `<target-owner>/<repo>` for `--repo` (extracted from the target remote URL).

### Option B: GitHub MCP (Fallback)

Only if `gh` CLI is unavailable, use `use_mcp_tool`:

- server_name: `"github"`
- tool_name: `"create_pull_request"`
- arguments: owner, repo, title, head, base, body, draft: true

**Save the PR number and URL from the response.**

## Step 4: Get Accurate PR Diff

Fetch the actual diff from GitHub:

```bash
gh pr diff <pr-number> --repo <target-owner>/<repo-name>
```

Or use GitHub MCP `get_pull_request_diff`.

## Step 5: Analyze Changes and Generate Content

Review the diff to understand:

- Modified files and their purposes
- Nature of changes (features, fixes, refactoring, docs, etc.)
- Scope and impact

**Note**: Don't force a single type prefix since PRs often contain mixed commit types.

### Title Format (max 72 characters)

Create a clear, descriptive title without forcing type prefixes:

- ✅ "Add user authentication and profile management"
- ✅ "Fix memory leak in data processing pipeline"
- ✅ "Update documentation and refactor API handlers"
- ❌ Don't use: "feat: Add feature" (too generic)

### Description Format

```markdown
## Summary
[2–3 sentence overview of what this PR accomplishes]

## Changes
- Key change 1
- Key change 2
- Key change 3

## Modified Files
- `path/to/file1.js` — Brief description
- `path/to/file2.py` — Brief description

## Breaking Changes
[List any breaking changes, or write "None"]

## Testing
[Describe how changes were tested]
```

## Step 6: Update PR with Real Title and Description

Update the draft PR:

### Option A: GitHub CLI

**CRITICAL: Escape special characters in `--body`:**

- Backticks → `\``
- Dollar signs → `\$`
- Double quotes → `\"`

```bash
gh pr edit <pr-number> \
  --repo <target-owner>/<repo-name> \
  --title "Your generated title" \
  --body "Description with \`escaped backticks\` and \$escaped dollars"

# Mark as ready for review
gh pr ready <pr-number> --repo <target-owner>/<repo-name>
```

### Option B: GitHub MCP (Fallback)

Use `update_pull_request` with `draft: false`.

## Step 7: Confirm Success

- Show the PR URL to the user.
- Summarize what was updated.
- Confirm the PR is ready for review.

## Important Rules

- **ALWAYS ask user for target branch** unless already specified.
- **NEVER assume target branch** — always confirm if not provided.
- **If multiple repos found in Step 0**: MUST ask user to select.
- **EXCLUDE current branch** from target options.
- **EXCLUDE `origin/HEAD`** from options.
- Run comprehensive git context command FIRST.
- **Create draft PR FIRST** with placeholder title to get accurate diff.
- Use `gh pr diff <pr-number>` for GitHub's accurate diff.
- Keep titles under 72 characters and descriptive.
- Update PR with real title/description after analysis.
- Mark PR as ready for review after updating.
- Handle errors gracefully and inform user.
