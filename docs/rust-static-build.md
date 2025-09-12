# Rust Static Build Workflow
This workflow builds a static Rust binary and creates a tarball containing the binary and its MD5 checksum. 
It follows the PacBio build pattern for single-binary projects.

## Quickstart

Add this workflow to your repository:

```yaml
name: Build Static Binary

on:
  workflow_dispatch:
    inputs:
      rust_version:
        description: 'Rust Docker image version'
        default: 'latest'
        type: string
      binary_name:
        description: 'Binary name (must match Cargo.toml)'
        required: true
        type: string

jobs:
  call-external:
    uses: holtjma/shared_github_workflows/.github/workflows/rust-static-build.yml@main
    with:
      rust_version: ${{ inputs.rust_version }}
      binary_name: ${{ inputs.binary_name }}
```

## What This Workflow Does

The `rust-static-build.yml` workflow performs the following steps:

1. **Checks out code** and gets binary build version
2. **Installs Rust target** for `x86_64-unknown-linux-gnu`
3. **Builds static binary** with static linking
4. **Creates tarball** with binary and MD5 checksum
5. **Uploads artifacts** for use by other workflows

## Workflow Details

### Inputs

- `rust_version` (optional): Rust Docker image version to use (default: `latest`)
- `binary_name` (required): Name of the binary to build (must match `Cargo.toml`)

### Outputs

- **Binary artifact**: `rust-binary-x86_64-linux` - The compiled static binary
- **Tarball artifact**: `rust-tarball-x86_64-linux` - Tarball with binary and MD5

### Build Configuration

- **Target**: `x86_64-unknown-linux-gnu`
- **Linking**: Static linking with `-C target-feature=+crt-static -C relocation-model=static`
- **Container**: Official Rust Docker image
- **Runner**: Ubuntu latest
