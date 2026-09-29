<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned 40-character commit SHA digests, making the action vulnerable to supply-chain attacks if the referenced tag is moved or overwritten:
- `uses: actions/setup-node@v5` (line 47)
- `uses: actions/cache@v4` (line 57)
- `uses: reviewdog/action-setup@v1` (line 63)
Each should be pinned to a full SHA, e.g. `actions/setup-node@<40-hex-sha> # v5`.

Locations:

- `action.yml:47`
- `action.yml:57`
- `action.yml:63`

### github-env-injection (severity: high)

A `run:` block writes user-controlled input values to `$GITHUB_ENV` without the required newline-stripping sanitization (`printf '%s' ... | tr -d '\n\r'`). The env vars `INPUT_FAIL_LEVEL` (sourced from `inputs.fail_level`) and `INPUT_FAIL_ON_ERROR` (sourced from `inputs.fail_on_error`) are written directly into `$GITHUB_ENV` via:
  `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`
  `echo "INPUT_FAIL_LEVEL=any" >> "${GITHUB_ENV}"`
  `echo "INPUT_FAIL_LEVEL=none" >> "${GITHUB_ENV}"`
Routing through an `env:` block does not sanitize the values. An attacker who controls `inputs.fail_level` could inject newlines to set arbitrary environment variables for subsequent steps.

Locations:

- `action.yml:75`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed three unpinned `uses:` references in action.yml by pinning them to full 40-character commit SHAs: actions/setup-node@v5 → @a0853c24544627f65ddf259abe73b1d18a591444, actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830, reviewdog/action-setup@v1 → @d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f. Fixed the github-env-injection finding by sanitizing the user-controlled INPUT_FAIL_LEVEL value with `printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r'` before writing it to $GITHUB_ENV. The literal constant values 'any' and 'none' written in the elif/else branches are safe and do not require sanitization.

