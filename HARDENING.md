<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved:
- `uses: actions/setup-node@v5`
- `uses: actions/cache@v4`
- `uses: reviewdog/action-setup@v1`
Each should be replaced with a full commit SHA (e.g. `actions/setup-node@11bd71901bbe5b1630ceea73d27597364c9af683 # v5`).

Locations:

- `action.yml:47`
- `action.yml:53`
- `action.yml:57`

### github-env-injection (severity: high)

A `run:` block writes the value of `INPUT_FAIL_LEVEL` — sourced from `inputs.fail_level` (an untrusted caller-controlled input) via an `env:` mapping — directly to `$GITHUB_ENV` without the required sanitization step (`printf '%s' "$VAR" | tr -d '\n\r'`). An attacker can inject newlines into `inputs.fail_level` to add arbitrary key=value pairs to the runner environment.

Offending line:
```
echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"
```

Fix:
```bash
safe=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')
echo "INPUT_FAIL_LEVEL=${safe}" >> "${GITHUB_ENV}"
```

Locations:

- `action.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed three unpinned `uses:` references by replacing mutable version tags with full 40-character commit SHAs: actions/setup-node@v5 → @a0853c24544627f65ddf259abe73b1d18a591444, actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830, reviewdog/action-setup@v1 → @d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f. Fixed github-env-injection by sanitizing INPUT_FAIL_LEVEL with `safe=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')` before writing to GITHUB_ENV, preventing newline injection attacks.

