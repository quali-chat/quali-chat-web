# Custom GitHub Actions Workflows

This directory contains custom GitHub Actions workflows that are separate from the upstream repository's workflows to avoid merge conflicts.

## Structure

- `.github_custom/workflows/` - Contains reusable workflow files
- `.github/workflows/custom.yml` - Minimal stub workflow that calls the custom workflows

## How it works

1. The stub workflow in `.github/workflows/custom.yml` triggers on push, pull_request, and workflow_dispatch events
2. It calls the custom workflows in `.github_custom/workflows/`
3. The custom workflows are self-contained and avoid conflicts with upstream
