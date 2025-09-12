# Create Release Workflow

This workflow creates a complete GitHub release with changelog notes and binary artifacts. It combines changelog extraction with static binary building to create a fully automated release process.

## Quickstart

Add this workflow to your repository:

```yaml
name: Create Release

on:
  workflow_dispatch:
    inputs:
      tag:
        description: 'Tag to create release for (e.g., v0.8.1)'
        required: true
        type: string
      rust_version:
        description: 'Rust Docker image version'
        default: 'latest'
        type: string
      fail_if_no_changelog:
        description: 'Fail if no changelog is found for the version'
        default: true
        type: boolean

jobs:
  call-external:
    uses: holtjma/shared_github_workflows/.github/workflows/create-release.yml@main
    with:
      tag: ${{ inputs.tag }}
      rust_version: ${{ inputs.rust_version }}
      fail_if_no_changelog: ${{ inputs.fail_if_no_changelog }}
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
```

## What This Workflow Does

The `create-release.yml` workflow performs the following steps:

1. **Extracts changelog section** for the specified version from `CHANGELOG.md`
2. **Builds static binary** using the `rust-static-build` workflow
3. **Creates GitHub release** with changelog notes and binary artifacts

## Workflow Details

### Inputs

- `tag` (required): The version tag to create a release for (e.g., `v0.8.1`)
- `rust_version` (optional): Rust Docker image version to use for building (default: `latest`)
- `fail_if_no_changelog` (optional): Whether to fail if no changelog is found (default: `true`)

### Outputs

- Creates a GitHub release with:
  - Release name: `{binary_name} {tag}`
  - Release body: Extracted changelog content
  - Binary artifacts: Tarball containing the static binary and MD5 checksum

### Changelog Format

The workflow expects your `CHANGELOG.md` to follow this format:

```markdown
# v0.8.1
## Changes
- New feature 1
- New feature 2

## Fixed
- Bug fix 1

# v0.8.0
## Changes
- Previous version changes...
```

The workflow will extract everything from the version header to the next version header (or end of file).
