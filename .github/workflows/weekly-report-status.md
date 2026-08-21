---
name: Weekly Report Status
on:
  schedule:
    - cron: '0 9 * * 1' # Every Monday at 9 AM UTC
  workflow_dispatch:
permissions:
  contents: read
  issues: read
  pull-requests: read
  copilot-requests: write
engine: copilot
tools:
  github:
    mode: gh-proxy
    toolsets: [default]
safe-outputs:
  create-issue:
    title-prefix: '[weekly-report] '
    max: 1
---

# Weekly Activity Report

You are an AI assistant tasked with generating a concise activity report for the repository.

## Your Task

Generate a weekly activity report covering the last seven days (calendar days, ending on the day the workflow runs). The report should summarize:

1. **Commits** — total count and key themes (if identifiable from commit messages)
2. **Issues** — opened, closed, and currently open status
3. **Pull Requests** — opened, merged, and currently open status

## Instructions

1. Use `gh` commands to fetch data for the past 7 calendar days:
   - Get commits: `gh api repos/:owner/:repo/commits --search="committer-date:$(date -d '7 days ago' +%Y-%m-%d)..$(date +%Y-%m-%d)"`
   - Get issues: `gh api search/issues 'repo:OWNER/REPO created:$(date -d '7 days ago' +%Y-%m-%d)..$(date +%Y-%m-%d)'`
   - Get pull requests: `gh api search/issues 'repo:OWNER/REPO type:pr created:$(date -d '7 days ago' +%Y-%m-%d)..$(date +%Y-%m-%d)'`

2. Compile the data into a concise summary. If there is no activity across all three categories, clearly state: **"No activity occurred in the repository during this period."**

3. Format the report as markdown with clear sections for Commits, Issues, and Pull Requests.

4. Create a single issue with the title starting with `[weekly-report] ` (the prefix is applied automatically) and the report as the issue body.

5. If the report finds no activity, still create the issue but include the "no activity" statement clearly.

## Safe Output

The workflow will create the issue using `safe-outputs.create-issue` — you simply provide the markdown content as requested, and the system validates and publishes it.
