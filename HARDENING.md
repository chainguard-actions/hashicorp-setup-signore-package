# Hardening Report: hashicorp--setup-signore-package/v1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **hashicorp--setup-signore-package/v1.1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in action.yml directly interpolate attacker-controlled `inputs.*` and `github.*` expressions inside shell commands instead of first assigning them to environment variables. This allows an attacker to inject arbitrary shell commands.

- Line 27: `echo "${{ inputs.token }}" | docker login -u ${{ github.actor }} --password-stdin ghcr.io` — both `inputs.token` and `github.actor` are interpolated directly.
- Line 28: `docker pull ghcr.io/hashicorp/signore:${{ inputs.version }}` — `inputs.version` is interpolated directly.
- Line 29: `CONTAINER_ID=$(docker create ghcr.io/hashicorp/signore:${{ inputs.version }})` — `inputs.version` is interpolated directly.
- Line 36: `[ -n "${{ inputs.signer }}" ] && echo "signer: ${{ inputs.signer }}" >> ~/.signore/config.yaml` — `inputs.signer` is interpolated directly.

Fix: assign each expression to an `env:` variable and reference it as `$VAR` in the shell script.

Locations:

- `action.yml:27`
- `action.yml:28`
- `action.yml:29`
- `action.yml:36`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.token }}" appears directly in run: block of step "login to ghcr, copy from image"; move to env: map

Locations:

- `action.yml:28`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step "login to ghcr, copy from image"; move to env: map

Locations:

- `action.yml:29`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step "login to ghcr, copy from image"; move to env: map

Locations:

- `action.yml:30`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.signer }}" appears directly in run: block of step "template signore config"; move to env: map

Locations:

- `action.yml:40`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.signer }}" appears directly in run: block of step "template signore config"; move to env: map

Locations:

- `action.yml:40`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed all script injection vulnerabilities in action.yml by moving ${{ }} expressions into env: blocks:

1. Step 'login to ghcr, copy from image': Added env block with INPUT_TOKEN (${{ inputs.token }}), GITHUB_ACTOR (${{ github.actor }}), and INPUT_VERSION (${{ inputs.version }}). Updated shell commands to use $INPUT_TOKEN, $GITHUB_ACTOR, and $INPUT_VERSION instead of direct expression interpolation.

2. Step 'template signore config': Added env block with INPUT_SIGNER (${{ inputs.signer }}). Updated shell command to use $INPUT_SIGNER instead of direct expression interpolation.

The third step ('get-version') had no expression interpolation in its run block and required no changes.

