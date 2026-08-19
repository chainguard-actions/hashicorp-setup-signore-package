<!-- markdownlint-disable -->

# Hardening Report: hashicorp--setup-signore-package/v1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **hashicorp--setup-signore-package/v1.1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple run: steps in action.yml directly interpolate ${{ }} expressions inside shell command strings, violating sub-rule (a). This allows an attacker-controlled value to be interpreted as shell code before the shell ever sees it.

• Line 28: `echo "${{ inputs.token }}" | docker login -u ${{ github.actor }} --password-stdin ghcr.io` — both `inputs.token` and `github.actor` are interpolated directly into the shell command.
• Line 29: `docker pull ghcr.io/hashicorp/signore:${{ inputs.version }}` — `inputs.version` is interpolated directly into the shell command.
• Line 30: `CONTAINER_ID=$(docker create ghcr.io/hashicorp/signore:${{ inputs.version }})` — `inputs.version` is interpolated directly inside a command substitution.
• Line 37: `[ -n "${{ inputs.signer }}" ] && echo "signer: ${{ inputs.signer }}" >> ~/.signore/config.yaml` — `inputs.signer` is interpolated twice directly into the shell command.

Fix: Move all ${{ }} values into env: variables and reference them as quoted shell variables (e.g., "$INPUT_VERSION") inside the run: block.

Locations:

- `action.yml:28`
- `action.yml:29`
- `action.yml:30`
- `action.yml:37`

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

Fixed all script injection findings in hardened/action/action.yml:

1. Step 'login to ghcr, copy from image': Moved ${{ inputs.token }}, ${{ github.actor }}, and ${{ inputs.version }} into an env: block as INPUT_TOKEN, GITHUB_ACTOR, and INPUT_VERSION respectively. Updated the run: block to reference these as quoted shell variables ($INPUT_TOKEN, "$GITHUB_ACTOR", $INPUT_VERSION).

2. Step 'template signore config': Moved ${{ inputs.signer }} into an env: block as INPUT_SIGNER. Updated the run: block to reference it as $INPUT_SIGNER.

No other findings (unpinned-uses, missing-permissions, etc.) were present in the findings list.

