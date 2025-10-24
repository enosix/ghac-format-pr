# JIRA PR Automation GitHub Action

This GitHub Action automatically formats pull requests with JIRA ticket information. When a PR is opened, it:

1. **Assigns the PR to the author**
2. **Adds JIRA ticket prefix to the title** (extracted from branch name)
3. **Links the JIRA ticket** in the PR description

## Inputs

- `github-token`: GitHub token for API access. Required. Default: `${{ github.token }}`
- `jira-base-url`: JIRA base URL (e.g., https://enosix.atlassian.net). Optional. Default: `https://enosix.atlassian.net`
- `ticket-prefix`: JIRA ticket prefix (e.g., PROD). Optional. Default: `PROD`
- `story-needed-label`: Label to use when no ticket found. Optional. Default: `[STORY NEEDED]`

## How It Works

The action extracts the JIRA ticket number from the branch name and:

- If a ticket is found (e.g., branch `PROD-1234-feature`):
  - Updates title to: `[PROD-1234] Original Title`
  - Adds JIRA link to description: `https://enosix.atlassian.net/browse/PROD-1234`
  
- If no ticket is found:
  - Updates title to: `[STORY NEEDED] Original Title`

The action skips updates if the PR title already has a ticket prefix or `[STORY NEEDED]` label.

## Usage

**Important**: This action must be triggered on `pull_request` with `types: [opened]` to work correctly.

### Basic Usage

```yaml
name: JIRA PR Automation
on:
  pull_request:
    types: [opened]

permissions:
  pull-requests: write
  contents: read

jobs:
  jira-automation:
    runs-on: ubuntu-latest
    steps:
      - name: Auto-format PR with JIRA ticket
        uses: enosix/ghac-format-pr@stable
```

### Custom Configuration

```yaml
name: JIRA PR Automation
on:
  pull_request:
    types: [opened]

permissions:
  pull-requests: write
  contents: read

jobs:
  jira-automation:
    runs-on: ubuntu-latest
    steps:
      - name: Auto-format PR with JIRA ticket
        uses: enosix/ghac-format-pr@stable
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          jira-base-url: 'https://yourcompany.atlassian.net'
          ticket-prefix: 'PROJ'
          story-needed-label: '[NO TICKET]'
```

## Examples

### Branch: `PROD-1234-add-new-feature`
**Before:**
- Title: `Add new feature`
- Body: `This adds a new feature for users`

**After:**
- Title: `[PROD-1234] Add new feature`
- Body: 
  ```
  This adds a new feature for users
  
  ---
  
  https://enosix.atlassian.net/browse/PROD-1234
  ```

### Branch: `add-new-feature` (no ticket)
**Before:**
- Title: `Add new feature`

**After:**
- Title: `[STORY NEEDED] Add new feature`
