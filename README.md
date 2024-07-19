# `Download Encrypted Artifact`

[![Test Download](https://github.com/AlexGidarakos/download-encrypted-artifact/actions/workflows/test.yml/badge.svg)](https://github.com/AlexGidarakos/download-encrypted-artifact/actions/workflows/test.yml)

A composite GitHub Action that downloads and decrypts [artifacts](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts) previously created with [upload-encrypted-artifact](https://github.com/AlexGidarakos/upload-encrypted-artifact). Internally powered by [@actions/download-artifact](https://github.com/actions/download-artifact).

- [`Download Encrypted Artifact`](#download-encrypted-artifact)
  - [Usage](#usage)
    - [Inputs](#inputs)
    - [Outputs](#outputs)
  - [Examples](#examples)
    - [Download an artifact](#download-an-artifact)
    - [Reset artifact retention period](#reset-artifact-retention-period)

## Usage

### Inputs

```yaml
- uses: AlexGidarakos/download-encrypted-artifact@v1
  with:
    # The GitHub token used to authenticate with the GitHub API
    # Optional, default is ${{ github.token }}
    github-token:

    # The repository owner and the repository name joined together by "/"
    # If github-token is specified, this is the repository that artifacts will be downloaded from
    # Optional, default is ${{ github.repository }}
    repository:

    # Name of the remote artifact in GitHub
    # Required
    remote-name:

    # Directory where the decrypted contents of the artifact will be extracted
    # Optional, default is $GITHUB_WORKSPACE
    destination:

    # Original text password used to encrypt the artifact before it was uploaded to GitHub
    # Required
    password:

    # If set to true, the action operates in a special mode that skips the decryption and decompression steps,
    # uploads the artifact again to GitHub and overwrites the old copy, resulting in a new artifact ID. This resets
    # the artifact's retention period and when run on a regular schedule, circumvents GitHub's artifact retention limits.
    # Optional, default is false
    reset-retention:
```

### Outputs

| Name | Description | Example |
| - | - | - |
| `artifact-id` | If reset-retention is true, this output will be the ID of the new artifact copy and can be used with the GitHub REST API | `1234` |
| `artifact-url` | If reset-retention is true, this output will be the URL to download the new artifact copy. Can be used in many scenarios such as linking to artifacts in issues or pull requests. Users must be logged-in in order for this URL to work. This URL is valid as long as the artifact has not expired or the artifact, run or repository have not been deleted | `https://github.com/example-org/example-repo/actions/runs/1/artifacts/1234` |

## Examples

### Download an artifact

```yaml
steps:
  - uses: AlexGidarakos/download-encrypted-artifact@v1
    with:
      github-token: ${{ github.token }}
      repository: ${{ github.repository }}
      remote-name: my-artifact
      destination: $GITHUB_WORKSPACE
      password: ${{ secrets.SuperSecret }}
```

### Reset artifact retention period

```yaml
steps:
  - uses: AlexGidarakos/download-encrypted-artifact@v1
    with:
      remote-name: my-artifact
      password: ${{ secrets.SuperSecret }}
      reset-retention: true
```
