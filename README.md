# Shared GitHub Workflows
This repository contains reusable GitHub Actions workflows for Rust projects.
These workflows help automate common tasks like building static binaries, creating releases, and updating release descriptions.
Some workflows expect certain file formats, see [docs](./docs/) for detailed descriptions of how to use each workflow.

## Available Workflows
| Workflow | Purpose | Outputs |
|----------|---------|---------|
| [Update Release](./docs/update-release.md) | Update existing release with changelog | Updated release description |
| [Create Release](./docs/create-release.md) | Create new release with binary | New release with binary artifacts |
| [Rust Static Build](./docs/rust-static-build.md) | Build static binary | Binary and tarball artifacts |
| [Rust Static Test](./docs/rust-static-test.md) | Test Rust project comprehensively | Test results and validation |

