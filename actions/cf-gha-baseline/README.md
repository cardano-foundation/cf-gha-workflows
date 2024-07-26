# Description

This action sets up a baseline for CF's engineering team GitHub Actions workflows. It includes the following features:

- Docker login to registries
- Sets common environment variables and github outputs so they can be leveraged by subsequent steps such as PR base branch, triggering branch, triggering tag, etc.
- Setup of typical workflow dependencies
- Provide a set of suggested docker image tags for the builds

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
    branches-ignore: [ main ]
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
```

