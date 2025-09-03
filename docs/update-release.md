# GitHub Actions Workflows
This action will parse a CHANGELOG.md file and update a release that is tagged as `v*` with the CHANGELOG information.
See below for formatting details.

## Quickstart
Add this workflow:
```
name: Update Release

on:
  push:
    tags:
      - 'v*'
  workflow_dispatch:
    inputs:
      tag:
        description: 'Tag to update (e.g., v0.8.1)'
        required: true
        type: string

jobs:
  call-external:
    uses: holtjma/shared_github_workflows/.github/workflows/update-release.yml@286c4ec0efbe7aac72a7149cfbd2a00f57457881
    permissions:
      contents: write
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
```

## Release Workflows
### `update-release.yml` - Update Release with Changelog
This workflow updates an existing release with changelog notes:

**Intended Triggers:** 
- When a tag matching `v*` is pushed (automatic)
- Manual workflow dispatch with tag input

**What it does:**
1. Extracts version information from the tag (either pushed or manually specified)
2. Finds the existing GitHub release for the tag
3. Extracts changelog notes from `CHANGELOG.md`
4. Updates the release description with the latest changelog content

## How to Use
### Automatic Release Updates
1. **Update your CHANGELOG.md** with the new version's changes
2. **Commit and push** your changes
3. **Create and push a tag:**
   ```bash
   git tag v0.8.1
   git push origin v0.8.1
   ```
4. **The workflow will automatically:**
   - Extract the version from the pushed tag
   - Find the existing release for that tag
   - Extract the changelog section for that version
   - Update the release description with the latest changelog content

### Manual Release Updates
You can also trigger the workflow manually:

1. Go to the **Actions** tab in your repository
2. Select the **Update Release** workflow
3. Click **Run workflow**
4. Enter the tag name (e.g., `v0.8.1`) in the input field
5. Click **Run workflow**

This is useful for:
- Updating releases for existing tags without re-pushing them
- Fixing release descriptions
- Adding changelog content to releases that were created without it

**Note:** You can run this workflow multiple times for the same tag to update the release description with the latest changelog content.

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

## Requirements

- `GITHUB_TOKEN` secret (automatically provided by GitHub)
- Your `CHANGELOG.md` should follow the expected format
- Tags should follow semantic versioning (e.g., `v0.8.1`)
- The release must already exist (you'll need to create it manually the first time)

## Notes

- The workflow automatically detects the version from the tag name (strips the `v` prefix)
- It handles both automatic tag pushes and manual workflow dispatch
- If no changelog is found for a version, it will note this in the release
- This workflow updates existing releases rather than creating new ones
- You can run this workflow multiple times for the same tag to refresh the release content
- The workflow adds a note indicating it was automatically updated from the tag
