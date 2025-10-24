# Format PR GitHub Action

This GitHub Action, named `Format PR`, is designed to enforce formatting rules for Pull Requests in your project. It provides a variety of inputs to customize the validation process, and outputs whether the PR formatting is valid.

## Inputs

- `github_token`: GitHub Token. Required.
- `pr_number`: Pull Request Number. Optional - if not provided, will use the PR from the triggering event.
- `title_format`: Title Format Regex. Optional - regex pattern that the PR title must match.
- `body_format`: Body Format Requirements. Optional - requirements for the PR body.
- `require_description`: Require PR Description. Optional, defaults to 'true'.
- `min_description_length`: Minimum Description Length. Optional, defaults to '10' characters.
- `block_on_fail`: Block PR if formatting fails. Optional, defaults to 'true'.
- `slack_webhook_url`: Slack Webhook URL to send notifications. Optional.

## Outputs

- `valid`: Whether the PR formatting is valid (true/false).

## Steps

1. Get PR Details: Retrieves the pull request information including title and body.
2. Validate PR Format: Validates the PR against the configured formatting rules.
3. Add Comment on Failure: Adds a comment to the PR if formatting validation fails.
4. Send notification to slack: Sends a notification to Slack if validation fails and a Slack webhook URL is provided.

## Usage

To use this action, include it in your workflow file with the necessary inputs. Here's an example:

```yaml
- name: Format PR
  uses: enosix/ghac-format-pr@stable
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    require_description: 'true'
    min_description_length: '20'
    title_format: '^\[.+\].+'
    block_on_fail: 'true'
    slack_webhook_url: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### Example Workflow

Here's a complete example workflow that validates PR formatting on pull request events:

```yaml
name: PR Format Check
on:
  pull_request:
    types: [opened, edited, synchronize]

permissions:
  pull-requests: write
  contents: read

jobs:
  format-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Validate PR Format
        uses: enosix/ghac-format-pr@stable
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          require_description: 'true'
          min_description_length: '20'
```

## Configuration Examples

### Require PR Title with Ticket Reference

```yaml
- name: Check PR Title
  uses: enosix/ghac-format-pr@stable
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    title_format: '^\[(JIRA-\d+|TICKET-\d+)\]'
```

### Require Detailed Description

```yaml
- name: Check PR Description
  uses: enosix/ghac-format-pr@stable
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    require_description: 'true'
    min_description_length: '50'
```

### With Slack Notifications

```yaml
- name: Format PR with Slack
  uses: enosix/ghac-format-pr@stable
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    require_description: 'true'
    slack_webhook_url: ${{ secrets.SLACK_WEBHOOK_URL }}
```

Make sure to set appropriate secrets in your repository settings.
