# Description

This action sets up a baseline for CF's engineering team GitHub Actions workflows. It includes the following features:

- Docker login to registries
- Sets common environment variables and github outputs so they can be leveraged by subsequent steps such as PR base branch, triggering branch, triggering tag, etc.
- Setup of typical workflow dependencies
- Provide a set of suggested docker image tags for the builds
- Discover an artifact version from a selected project directory

# Usage

We recommend using these triggers as default as this action handles this workflow logic and provides docker tags for all the possible scenarios such as:

- PR artifacts
- Branch artifacts
- Release artifacts (main, tags)

```yaml
name: Example Workflow

on:
  push:
    branches: [ main, develop ]
    tags:
      - '[0-9]+.[0-9]+.[0-9]+*'
  pull_request:
    types: [ opened, synchronize ]
  workflow_dispatch:

jobs:
  mypipeline:
    permissions:
      contents: write
      packages: write
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v4

    - name: ⛮ cf-gha-baseline
      uses: cardanofoundation/cf-gha-workflows/./actions/cf-gha-baseline@main
      with:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        PRIVATE_DOCKER_REGISTRY_URL: ${{ secrets.GITLAB_DOCKER_REGISTRY_URL }}
        PRIVATE_DOCKER_REGISTRY_USER: Deploy-Token
        PRIVATE_DOCKER_REGISTRY_PASS: ${{ secrets.GITLAB_PKG_REGISTRY_TOKEN }}
        HUB_DOCKER_COM_USER: ${{ secrets.HUB_DOCKER_COM_USER }}
        HUB_DOCKER_COM_PASS: ${{ secrets.HUB_DOCKER_COM_PASS }}
        DOCKER_REGISTRIES: "${{ secrets.DOCKER_REGISTRIES }}"
        # Optional for monorepos. Version metadata is read from this directory.
        artifact-version-directory: path/to/project
```

`artifact-version-directory` is resolved relative to `working-directory` and
defaults to that directory. Point it at the project that owns the artifact when
a repository contains multiple independently versioned projects. The action
currently detects versions from `build.gradle.kts`, `gradle.properties`,
`pom.xml`, `package.json`, and the `[package]` section of `Cargo.toml`.

For a component-scoped release tag such as `gateway/v1.2.3`, the action keeps
the full tag in `TAG_NAME` but emits the Docker-safe `v1.2.3` in its suggested
image tags.

# Example outputs

* Branch builds:

![image](https://github.com/user-attachments/assets/fa85609d-0861-4741-b3fa-873b27dac843)

* PR builds:

![image](https://github.com/user-attachments/assets/0275b24e-b296-4eab-a591-054d6727e6bf)

* Tag builds:

![image](https://github.com/user-attachments/assets/6fbaccb7-12c7-4f13-9140-273686d13719)
