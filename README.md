# Myco Assistant

[![Sync Markdown Issues](https://github.com/cr-nattress/mycoassistant/actions/workflows/sync-issues.yml/badge.svg?branch=main)](https://github.com/cr-nattress/mycoassistant/actions/workflows/sync-issues.yml)

A repo for planning and automating issue creation from markdown.

## Structure
- `.github/issues/` — Issue specs as Markdown with YAML frontmatter
- `.github/workflows/sync-issues.yml` — Workflow to sync Markdown issues to GitHub Issues
- `.github/ISSUE_TEMPLATE/` — Bug report and feature request templates
- `.github/PULL_REQUEST_TEMPLATE.md` — Pull request template
- `plans/issues.csv` — Source CSV for generating issue files
- `prompts/` — Team prompts and rules

## Workflows
- "Sync Markdown Issues" runs on push/PR changes to `.github/issues/**` or the workflow file.
- It parses frontmatter (`title`, `labels`) and creates issues if they don’t already exist.

## Usage
1. Edit or add markdown files in `.github/issues/`.
2. Push changes to GitHub.
3. Check the Actions tab to verify sync and the Issues tab for results.

## Notes
- Duplicate protection: it searches for an existing issue with the same exact title.
- Labels listed in frontmatter will be applied automatically.
