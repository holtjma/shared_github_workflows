# Rust Static Test Workflow

This workflow runs comprehensive tests on a Rust project, including static analysis, documentation tests, and static binary tests to ensure code quality.

## Quickstart

Add this workflow to your repository:

```yaml
name: Test Rust Project

on:
  workflow_dispatch:
    inputs:
      rust_version:
        description: 'Rust Docker image version'
        default: 'latest'
        type: string
      test_docs:
        description: 'If true, test the docs'
        default: true
        type: boolean

jobs:
  call-external:
    uses: holtjma/shared_github_workflows/.github/workflows/rust-static-test.yml@main
    with:
      rust_version: ${{ inputs.rust_version }}
      test_docs: ${{ inputs.test_docs }}
```

## What This Workflow Does

The `rust-static-test.yml` workflow performs comprehensive testing:

1. **Runs Clippy** for static analysis and linting
2. **Tests documentation** (if enabled)
3. **Tests static binary** with static linking enabled
4. **Ensures code quality** through multiple testing approaches

## Workflow Details

### Inputs

- `rust_version` (optional): Rust Docker image version to use (default: `latest`)
- `test_docs` (optional): Whether to run documentation tests (default: `true`)

### Outputs

- **No artifacts produced** - This workflow is for testing only
- **Test results** are displayed in the workflow logs
- **Fails on errors** - Workflow will fail if any tests fail

### Test Types

1. **Clippy Static Analysis**
   - Runs `cargo clippy` with `-D warnings` to catch linting issues
   - Automatically installs clippy component if not present

2. **Documentation Tests**
   - Runs `cargo test --release --doc` to test code examples in documentation
   - Only runs if `test_docs` is `true`

3. **Static Binary Tests**
   - Runs `cargo test --release --all-targets --target x86_64-unknown-linux-gnu -- --include-ignored`
   - Uses static linking flags: `-C target-feature=+crt-static -C relocation-model=static`
   - Tests all targets including ignored tests

## How to Use

### Manual Testing

1. **Go to Actions tab** in your repository
2. **Select "Rust Static Tests" workflow**
3. **Click "Run workflow"**
4. **Optionally specify:**
   - Rust version (if different from latest)
   - Whether to test documentation (default: true)
5. **Click "Run workflow"**

### As Part of CI/CD Pipeline

This workflow is typically used in CI/CD pipelines:

```yaml
name: CI Pipeline

on: [push, pull_request]

jobs:
  test:
    uses: holtjma/shared_github_workflows/.github/workflows/rust-static-test.yml@main
    with:
      rust_version: 'latest'
      test_docs: true
```

### Integration with Other Workflows

```yaml
jobs:
  test:
    uses: holtjma/shared_github_workflows/.github/workflows/rust-static-test.yml@main
    with:
      rust_version: 'latest'
  
  build:
    needs: test
    uses: holtjma/shared_github_workflows/.github/workflows/rust-static-build.yml@main
    with:
      binary_name: 'my-tool'
```

## Requirements

- **Rust project**: Repository must have `Cargo.toml` and Rust source code
- **Repository ownership**: Repository must be owned by the user (required for `git describe`)
- **Test code**: Project should have tests written (unit tests, integration tests, doc tests)

## Static Linking Configuration

The workflow uses these Rust flags for static linking tests:

```bash
RUSTFLAGS="-C target-feature=+crt-static -C relocation-model=static"
```

This ensures tests run with the same static linking configuration used in production builds.

## Error Handling

- **Clippy warnings**: Workflow fails if clippy finds any warnings (due to `-D warnings`)
- **Test failures**: Workflow fails if any tests fail
- **Documentation test failures**: Workflow fails if doc tests fail (when enabled)
- **Build failures**: Workflow fails if the project doesn't compile

## Test Coverage

The workflow provides comprehensive test coverage:

- **Static analysis**: Catches potential bugs and code quality issues
- **Documentation**: Ensures code examples in docs are correct
- **Static binary**: Tests the actual binary that will be distributed
- **All targets**: Tests unit tests, integration tests, and benchmarks
- **Ignored tests**: Includes tests marked with `#[ignore]`

## Notes

- **No artifacts**: This workflow doesn't produce any build artifacts
- **Testing only**: Focused on code quality and correctness
- **Static linking**: Tests run with static linking to match production builds
- **Comprehensive**: Covers multiple aspects of code quality
- **CI/CD ready**: Designed to be used in continuous integration pipelines

## Maintenance Notes

- **Multi-binary projects**: Currently tests all binaries; may need updates for specific binary testing
- **Target architecture**: Currently hardcoded to `x86_64-unknown-linux-gnu`
- **Docker image**: Uses official Rust Docker image; can be customized via `rust_version` input

## Troubleshooting

### Common Issues

1. **"Clippy warnings found"**: Fix the linting issues reported by clippy
2. **"Doc tests failed"**: Check that code examples in your documentation are correct
3. **"Tests failed"**: Fix failing unit or integration tests
4. **"Repository not owned by user"**: Ensure the repository is owned by the user running the workflow

### Performance Tips

- **Disable doc tests**: Set `test_docs: false` if doc tests are slow or unnecessary
- **Use specific Rust version**: Pin to a specific Rust version for consistent results
- **Run in parallel**: This workflow can run in parallel with other workflows

## Integration Examples

### With Build Workflow

```yaml
jobs:
  test:
    uses: holtjma/shared_github_workflows/.github/workflows/rust-static-test.yml@main
  
  build:
    needs: test
    uses: holtjma/shared_github_workflows/.github/workflows/rust-static-build.yml@main
    with:
      binary_name: 'my-tool'
```

### With Release Workflow

```yaml
jobs:
  test:
    uses: holtjma/shared_github_workflows/.github/workflows/rust-static-test.yml@main
  
  create-release:
    needs: test
    uses: holtjma/shared_github_workflows/.github/workflows/create-release.yml@main
    with:
      tag: 'v1.0.0'
      binary_name: 'my-tool'
      release_name: 'My Tool'
```
