# Create Library Release Workflow

This workflow creates a complete GitHub release with changelog notes for projects that do not ship a binary, such as library crates.
It is the same as [Create Release](./create-release.md) except that it runs the test workflow instead of the static build, and the release has no attached assets.

## Quickstart

Add this workflow to your repository:

```yaml
name: Create Library Release

on:
  workflow_dispatch:
    inputs:
      tag:
        description: 'Tag to create release for (e.g., v0.8.1)'
        required: true
        type: string
      release_name:
        description: 'Release name (default: Release)'
        default: 'Release'
        type: string
      rust_version:
        description: 'Rust Docker image version'
        default: 'latest'
        type: string
      test_docs:
        description: 'If true, test the docs'
        default: true
        type: boolean
      fail_if_no_changelog:
        description: 'Fail if no changelog is found for the version'
        default: true
        type: boolean

jobs:
  call-external:
    uses: holtjma/shared_github_workflows/.github/workflows/create-lib-release.yml@main
    with:
      tag: ${{ inputs.tag }}
      release_name: ${{ inputs.release_name }}
      rust_version: ${{ inputs.rust_version }}
      test_docs: ${{ inputs.test_docs }}
      fail_if_no_changelog: ${{ inputs.fail_if_no_changelog }}
```

## What This Workflow Does

The `create-lib-release.yml` workflow performs the following steps:

1. **Extracts changelog section** for the specified version from `CHANGELOG.md`
2. **Tests the project** using the `rust-static-test` workflow (clippy, doc tests, static tests)
3. **Creates tag** if it doesn't already exist
4. **Creates GitHub release** with changelog notes and no attached assets

## Workflow Details

### Inputs

- `tag` (required): The version tag to create a release for (e.g., `v0.8.1`)
- `release_name` (optional): The name for the release (default: `Release`)
- `rust_version` (optional): Rust Docker image version to use for testing (default: `latest`)
- `test_docs` (optional): Whether to run documentation tests (default: `true`)
- `fail_if_no_changelog` (optional): Whether to fail if no changelog is found (default: `true`)

### Outputs

- Creates a GitHub release with:
  - Release name: `{release_name} {tag}`
  - Release body: Extracted changelog content
  - No release assets

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

## When to Use This Instead of Create Release

| Use | Workflow |
|-----|----------|
| Project builds a binary that should be attached to the release | [Create Release](./create-release.md) |
| Project is a library with no binary to distribute | Create Library Release (this workflow) |
| A release already exists and only its description needs updating | [Update Release](./update-release.md) |
