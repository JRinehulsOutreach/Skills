---
name: smart-pr
description: Reviews changes between the current branch and a target branch, fetches Trello card data, generates a pull request title and description, gets user approval, and creates the PR. Use this when asked to create a pull request, open a PR, or submit changes for review.
---

## Smart PR Workflow

Follow these steps precisely when this skill is invoked.

### Step 1: Gather Inputs

Ask the user for the following if not already provided in the prompt:

1. **Target branch** — the branch to merge into (e.g., `main`, `develop`)
2. **Trello card URL(s)** — one or more URLs in the format `https://trello.com/c/{cardId}/...`

### Step 2: Verify Trello Credentials

Check that the environment variables `TRELLO_API_KEY` and `TRELLO_TOKEN` are set.

- On Windows, run: `[System.Environment]::GetEnvironmentVariable('TRELLO_API_KEY', 'User')`
- If either is missing, tell the user:
  > "Trello credentials are not configured. Please set `TRELLO_API_KEY` and `TRELLO_TOKEN` as user-level environment variables and restart the terminal."
  Then abort.

### Step 3: Fetch Trello Card Data

For each Trello card URL:

1. Extract the card ID — it is the path segment immediately after `/c/` in the URL (e.g., `https://trello.com/c/abc123/my-card` → card ID is `abc123`)
2. Call the Trello REST API:
   ```
   GET https://api.trello.com/1/cards/{cardId}?key={TRELLO_API_KEY}&token={TRELLO_TOKEN}
   ```
3. From the JSON response, capture:
   - `name` — the card title (used as anchor text)
   - `url` — the canonical card URL (used as href)

### Step 4: Analyze the Diff

Run the following to understand what changed:

```
git log {target}..HEAD --oneline
git diff {target}...HEAD --stat
```

Use the commit history and file changes — along with the Trello card titles — to understand the purpose and scope of the changes.

### Step 5: Draft the PR Title and Description

**PR Title**: Use the primary (first) Trello card's `name`, cleaned up as a concise title. If no Trello cards are provided, derive the title from the branch name (replace hyphens with spaces, title-case it).

**PR Description** (use this exact structure):

```markdown
## Problem
<A concise summary of the problem(s) solved, informed by the Trello card titles and the nature of the diff>

## Solution
<An overview of how the problem(s) were solved, based on the code changes>

## Trello Cards
<a href="{card.url}">{card.name}</a>
<a href="{card.url}">{card.name}</a>
```

- Each Trello card gets its own `<a>` tag on its own line, using the card's `url` as the `href` and the card's `name` as the link text.
- If no Trello cards are provided (rare), omit the `## Trello Cards` section.

### Step 6: Present Draft and Request Approval

Show the proposed PR title and description clearly to the user in a code block. Then ask:

> Does this PR look good? Reply **yes** to create it, or suggest changes you'd like made.

If the user suggests changes, incorporate their feedback, show the revised draft, and ask again. Repeat until approved.

### Step 7: Push Branch if Needed

Check whether the current branch has been pushed to the remote:

```
git status -sb
```

If there is no upstream tracking branch (no `origin/...` shown), push it:

```
git push -u origin {current-branch}
```

### Step 8: Create the Pull Request

Run:

```
gh pr create --base {target} --title "{title}" --body "{description}"
```

Confirm success by showing the PR URL from the output.
