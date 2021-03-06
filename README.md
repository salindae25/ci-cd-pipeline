## Example Usage

workflow file using PaulHatch/semantic-version
with some modification

```yaml
name: develop
on:
  push:
    branches:
      - develop

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          # Fetch all commits
          fetch-depth: 0

      - name: Semantic versioning
        id: versioning
        uses: PaulHatch/semantic-version@v4.0.2
        with:
          branch: develop
          tag_prefix: "v"
          major_pattern: "BREAKING CHANGE:"
          minor_pattern: "feat:"
          format: "v${major}.${minor}.${patch}-QA${increment}"
          bump_each_commit: false

      - name: Create Release
        if: ${{ !startsWith(github.ref, 'refs/tags/') }}
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.github_token }}
        with:
          tag_name: ${{ steps.versioning.outputs.version }}
          release_name: ${{ steps.versioning.outputs.version }}
          prerelease: true
```

for staging in release branch

```yaml
name: staging
on:
  push:
    branches: [release/*]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          # Fetch all commits
          fetch-depth: 0

      - name: Semantic versioning
        id: versioning
        uses: PaulHatch/semantic-version@v4.0.2
        with:
          branch: release/*
          tag_prefix: "v"
          major_pattern: "BREAKING CHANGE:"
          minor_pattern: "feat:"
          format: "v${major}.${minor}.${patch}-UAT${increment}"
          bump_each_commit: false

      - name: Create Release
        if: ${{ !startsWith(github.ref, 'refs/tags/') }}
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.github_token }}
        with:
          tag_name: ${{ steps.versioning.outputs.version }}
          release_name: ${{ steps.versioning.outputs.version }}
          prerelease: true
```
