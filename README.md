# Draft Release Action

This action creates a draft release for the next version to be released. It reads the release file in `.github/release.yml` and creates a draft release with the next version number based on the current version number and the labels of the pull requests merged since the last release.

To use this action, you need to create a release file in `.github/release.yml` as shown in the GitHub documentation for [creating a release file](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes#configuring-automatically-generated-release-notes).

To decide whether the next release should be a major or minor release, the action looks at the labels of the pull requests merged since the last release. If there is at least one pull request with the label specified in the `major-label` input, the next release will be a major release. Otherwise, if there is at least one pull request with the label specified in the `minor-label` input, the next release will be a minor release. Otherwise, the next release will be a patch release.

## Simple Usage

```yaml
name: Draft Release

on:
  push:
    branches:
      - main

jobs:
  draft-release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: lucacome/draft-release@v0.1.0
        with:
          minor-label: 'enhancement'
          major-label: 'change'

```

## Inputs

| Name | Type | Description |
| --- | --- | --- |
| `minor-label` | `string` | The label to use for minor releases. (default `enhancement`) |
| `major-label` | `string` | The label to use for major releases. (default `change`) |
| `notes-header` | `string` | The header to use for the release notes. |
| `notes-footer` | `string` | The footer to use for the release notes. |
| `github-token` | `string` | The GitHub token to use for the release. (default `github.token`) |

## Outputs

| Name | Type | Description |
| --- | --- | --- |
| `version` | `string` | The version number of the next release. |
| `release-id` | `string` | The ID of the next release. |
| `release-notes` | `string` | The release notes of the next release. |
| `release-url` | `string` | The URL of the next release. |

## Header and Footer

The header and footer have two special placeholders that will be replaced with the version number of the next release:
- `{{version}}` will be replaced with the version number of the next release.
- `{{version-number}}` will be replaced with the version number of the next release without the `v` prefix.

## Examples

### Add Footer

```yaml
name: Draft Release

on:
  push:
    branches:
      - main

jobs:
  draft-release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: lucacome/draft-release@v0.1.0
        with:
          minor-label: 'enhancement'
          major-label: 'change'
          notes-footer: |
            This is a footer.
            It can be multiline.
```

### Get Version Number of Next Release

```yaml
name: Draft Release

on:
  push:
    branches:
      - main

jobs:
  draft-release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: lucacome/draft-release@v0.1.0
        id: draft-release
        with:
          minor-label: 'enhancement'
          major-label: 'change'

      - name: Get Version Number
        run: echo ${{ steps.draft-release.outputs.version }}
