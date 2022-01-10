## Example Usage

With options file

```yaml
name: Manage versions

on: [push]

jobs:
  bump:
    runs-on: ubuntu-latest

    steps:
      # Checkout action is required
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v1
        with:
          node-version: "12"
      - name: Bump Versions
        uses: michmich112/version-bumper@master
        with:
          options-file: "./examples/example-options-full.json"
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

option file

```json
{
  "scheme": "semantic",

  // file where the current version can be found
  "versionFile": "./package.json",
  "files": [
    {
      "path": // path to file containing version to be updated
      "line": // optional line number if needed
    }
  ],
  "rules": [
    {
      "trigger": "commit",
      "branch": "hotfix",
      "bump": "build"
    },
    {
      "trigger": "commit",
      "bump": "minor",
      "branch": "master",
      "reset": "build"
    },
    {
      "trigger": "commit",
      "bump": "major",
      "branch": "release",
      "reset": [
        "minor",
        "build"
      ]
    }
  ]}
```

Without options file

```yaml
name: Manage versions

on: [push, pull_request]

jobs:
  bump:
    runs-on: ubuntu-latest

    steps:
      # Checkout action is required
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v1
        with:
          node-version: "12"
      - name: Bump Versions
        uses: michmich112/version-bumper@master
        with:
          scheme: semantic
          username: DoomGuy
          email: guy@doom.id
          version-file: "./package.json"
          files: >
            [
              "./version-list.json"
            ]
          rules: >
            [{
              "trigger":"commit",
              "branch": "staging",
              "suffix": "-beta",
              "bump":"build"
            },{
              "trigger": "pull-request",
              "destBranch": "main",
              "suffix": "-rc",
              "bump": "minor",
              "tag": true
            }, {
              "trigger":"commit",
              "branch":"release",
              "bump":"major",
              "tag": true,
              "reset":["minor","build"]
            }]'
          github-token: ${{ secrets.GITHUB_TOKEN }}
```
