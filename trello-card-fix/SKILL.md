---
name: trello-card-fix
description: Read a Trello card via HTTP GET, propose a solution, and implement it in the codebase on the current branch. No branch creation. No commit.
---

## trello-card-fix Workflow

Follow these steps precisely when this skill is invoked.

### Step 1: Get the Card ID

If the user has not already provided a Trello card ID or URL, ask for one now.

- Accept either a short ID (e.g., `MgFSFpk0`) or a full URL (e.g., `https://trello.com/c/MgFSFpk0/66-my-card`)
- If a URL is provided, extract the card ID — it is the path segment immediately after `/c/` (e.g., `MgFSFpk0`)

### Step 2: Verify Trello Credentials

Check that `TRELLO_API_KEY` and `TRELLO_TOKEN` are set as user-level environment variables.

On Windows:
```powershell
[System.Environment]::GetEnvironmentVariable('TRELLO_API_KEY', 'User')
[System.Environment]::GetEnvironmentVariable('TRELLO_TOKEN', 'User')
```

If either is missing, tell the user:
> "Trello credentials are not configured. Please set `TRELLO_API_KEY` and `TRELLO_TOKEN` as user-level environment variables and restart the terminal."

Then abort.

### Step 3: Fetch the Card

Call the Trello REST API using `Invoke-RestMethod` (no trello-cli needed):

```powershell
$key   = [System.Environment]::GetEnvironmentVariable('TRELLO_API_KEY', 'User')
$token = [System.Environment]::GetEnvironmentVariable('TRELLO_TOKEN', 'User')
$card  = Invoke-RestMethod -Uri "https://api.trello.com/1/cards/{cardId}?key=$key&token=$token"
```

Capture `$card.name`, `$card.desc`, and `$card.url`.

Also fetch any checklists (acceptance criteria often live here):

```powershell
$checklists = Invoke-RestMethod -Uri "https://api.trello.com/1/cards/{cardId}/checklists?key=$key&token=$token"
```

### Step 4: Display the Card Summary

Show the user a clear summary:
- **Card name**
- **Description**
- **Checklist items** (if any), noting which are checked vs. unchecked

### Step 5: Locate Relevant Code

Search the codebase to identify where the card's work applies. Use `grep`, `glob`, and `view` tools as needed. Consider:
- Which files are affected by the described problem?
- Are there existing components, hooks, or utilities related to the feature?

### Step 6: Ask Clarifying Questions (if needed)

If the card description is ambiguous, incomplete, or the correct implementation approach is unclear:
- Ask the user targeted clarifying questions **one at a time**
- Do not proceed to implementation until ambiguities are resolved

If the card cannot be implemented at this time (e.g., missing dependencies, unclear requirements), explain why and ask how to proceed.

### Step 7: Propose a Solution

Before writing any code, describe your proposed approach clearly:
- What files will be changed and how
- Any trade-offs or notable decisions

Then ask the user:
> Does this approach look good? Reply **yes** to proceed, or suggest changes.

If the user suggests changes, update the proposal and ask again. Repeat until approved.

### Step 8: Implement the Changes

Once the user approves:
- Make all changes on the **current branch** — do **not** create a new branch
- Apply changes surgically; do not modify unrelated code

### Step 9: Summarize

When done, tell the user:
- A brief summary of what was changed and why
- That no commit has been made — they can review the diff and commit using the `smart-commit` skill (or manually) when ready
