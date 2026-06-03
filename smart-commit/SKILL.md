---
name: smart-commit
description: Reviews unstaged and staged changes since the last commit, generates a commit message, gets user approval, and commits when approved. Use this when asked to commit changes, create a commit, or stage and commit.
---

## Smart Commit Workflow

Follow these steps precisely when this skill is invoked:

### Step 1: Review Changes

Run `git diff HEAD` to review all changes (staged and unstaged) since the last commit. Also run `git status` to understand which files are new, modified, or deleted.

### Step 2: Generate a Commit Message

Based on the diff, write a commit message that:
- Has a concise subject line (50 characters or fewer) in imperative mood (e.g., "Add", "Fix", "Update", not "Added", "Fixed")
- Includes a blank line followed by a descriptive body (if the changes warrant it) explaining *what* changed and *why*
- Includes the Co-authored-by trailer at the end:
  `Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>`

### Step 3: Present and Request Approval

Show the proposed commit message to the user clearly, formatted in a code block. Then ask:

> Does this commit message look good? Reply **yes** to approve and commit, or suggest changes you'd like made.

### Step 4: Handle Response

- **If approved** (user says yes, looks good, approve, LGTM, or similar): proceed to Step 5.
- **If the user suggests changes**: incorporate their feedback, show the revised message in a code block, and ask for approval again. Repeat until approved.

### Step 5: Stage and Commit

Once approved:
1. Run `git add -A` to stage all changes.
2. Run `git commit -m "<subject>" -m "<body with trailer>"` using the approved message.
3. Confirm success by showing the output of the commit command.
