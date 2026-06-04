# Copilot Skills

This repository contains user-level skills for the GitHub Copilot CLI. Skills are reusable workflows that teach Copilot how to perform specific multi-step tasks.

## What Are Skills?

Each skill lives in its own folder and contains a `SKILL.md` file that defines the workflow. When you invoke a skill by name (e.g., "use the smart-commit skill"), Copilot reads the `SKILL.md` and follows its steps precisely.

## Available Skills

| Skill | Description |
|-------|-------------|
| `smart-commit` | Reviews all changes since the last commit, generates a commit message, gets your approval, and commits. |
| `smart-pr` | Reviews branch changes, fetches Trello card data, generates a PR title and description, gets your approval, and opens the PR via `gh`. |
| `trello-card-fix` | Reads a Trello card via HTTP GET, locates the relevant code, proposes a solution, and implements it on the current branch. No branch creation or commit. |

## Prerequisites

### GitHub CLI (`gh`)

The `smart-pr` skill uses the `gh` CLI to create pull requests. Install it from [cli.github.com](https://cli.github.com) and authenticate with `gh auth login`.

### Trello API Credentials

The `smart-pr` and `trello-card-fix` skills both call the Trello REST API directly — no third-party CLI required. You need a **Trello API Key** and a **Trello Token** set as user-level environment variables.

#### How to get your Trello API Key and Token

1. Go to [https://trello.com/power-ups/admin](https://trello.com/power-ups/admin) and create a new Power-Up (or use an existing one).
2. Under the Power-Up's **API Key** tab, copy your API Key.
3. On that same page, click **"Token"** to generate a personal token. Grant it read/write access and copy the token value.

#### Setting the environment variables (Windows)

Open a PowerShell prompt and run:

```powershell
[System.Environment]::SetEnvironmentVariable('TRELLO_API_KEY', '<your-api-key>', 'User')
[System.Environment]::SetEnvironmentVariable('TRELLO_TOKEN',   '<your-token>',   'User')
```

Restart your terminal after setting them. To verify they are set:

```powershell
[System.Environment]::GetEnvironmentVariable('TRELLO_API_KEY', 'User')
[System.Environment]::GetEnvironmentVariable('TRELLO_TOKEN',   'User')
```

## Adding a New Skill

1. Create a new folder under `skills/` with your skill name (e.g., `my-skill/`).
2. Add a `SKILL.md` file with a YAML front matter block (`name` and `description`) followed by the workflow steps.
3. Copilot will automatically pick it up when invoked by name.
