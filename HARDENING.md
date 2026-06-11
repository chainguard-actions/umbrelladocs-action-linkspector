<!-- markdownlint-disable -->

# Hardening Report: umbrelladocs--action-linkspector/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **umbrelladocs--action-linkspector/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three external actions using mutable tag refs instead of immutable 40-character SHA commit digests. This exposes the action to supply-chain attacks if any of those tags are moved or the upstream repository is compromised. Failing references: `actions/setup-node@v5`, `actions/cache@v4`, `reviewdog/action-setup@v1`.

Locations:

- `action.yml:44`
- `action.yml:51`
- `action.yml:57`

### github-env-injection (severity: high)

A `run:` block writes the user-controlled input `inputs.fail_level` (via env var `INPUT_FAIL_LEVEL`) directly to `$GITHUB_ENV` without the required sanitization step (`printf '%s' "$VAR" | tr -d '\n\r'`). An attacker can inject newlines into `inputs.fail_level` to smuggle arbitrary key=value pairs into the GitHub environment, potentially overwriting sensitive environment variables for subsequent steps. The same block also branches on `INPUT_FAIL_ON_ERROR` (from `inputs.fail_on_error`) and writes `INPUT_FAIL_LEVEL=any` or `INPUT_FAIL_LEVEL=none` to `$GITHUB_ENV`; while those branch values are literals, the first branch (`echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`) directly reflects the unsanitized user input.

Locations:

- `action.yml:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed three unpinned action references in action.yml: actions/setup-node@v5 → @a0853c24544627f65ddf259abe73b1d18a591444, actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830, reviewdog/action-setup@v1 → @d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f. Each retains the original tag as a comment. Fixed github-env-injection by sanitizing the user-controlled INPUT_FAIL_LEVEL value with `printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r'` before writing it to $GITHUB_ENV, preventing newline injection attacks.

