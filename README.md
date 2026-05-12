## What

A [setup-signore](https://github.com/hashicorp/setup-signore) alternative for Linux GitHub Runners.
Extracts a binary from the Signore Docker image on GitHub Packages to the local disk.

## Why

- the setup-signore GitHub Action requires users to supply a GitHub personal access token
- this version automatically uses the `GITHUB_TOKEN` vended to a running Action to authenticate to the GitHub container registry (ghcr.io)

## Limitations

- Linux only, because macOS workers do not have Docker installed, and Windows workers have other unexplored issues
  - Incidentally, look how [fun](https://github.com/moby/moby/blob/master/contrib/download-frozen-image-v2.sh) it is to "pull" a Docker image without Docker

## Permissions

One security best practice to keep in mind when configuring your GitHub Actions is [least privilege](https://csrc.nist.gov/glossary/term/least_privilege). GitHub grants a [wide variety of scopes](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token) to a GitHub Action's `GITHUB_TOKEN` by default. Whenever possible, you should specify granularly scoped permissions to confine your workflows to their intended uses.

If specify granular permissions, be sure to include the `package: read` scope. This allows your action to use its `GITHUB_TOKEN` to pull the `signore` Docker image from the GitHub Container Registry.

```yaml
permissions:
  packages: read
```

Read more about [permissions for `GITHUB_TOKEN` in the official docs](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token).

## How

Add a step to your workflow like so:

```yaml
    steps:
      - name: install signore
        uses: hashicorp/setup-signore-package@v1
```

With no inputs, the `GITHUB_TOKEN` is passed automatically, and a default version of `signore` is used.

Optionally, `token`, `version`, and `signer` can be set:

```
    steps:
      - uses: hashicorp/setup-signore-package@v1
        with:
          version: v0
          signer: test_signer
          token: ${{ secrets.GITHUB_TOKEN }}
```

## Privacy

This Action contacts Chainguard's licensing server to verify authorization. Connection metadata (IP address, GitHub repository identifier, timestamp, and any metadata encoded in the auth token) is transmitted to Chainguard, Inc. even if authorization is denied in accordance with our [Privacy Notice](https://www.chainguard.dev/legal/privacy-notice)
