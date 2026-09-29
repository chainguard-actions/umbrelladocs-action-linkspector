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
- `actions/setup-node@v5` (line 49)
- `actions/cache@v4` (line 55)
- `reviewdog/action-setup@v1` (line 60)
Each should be replaced with a full commit SHA, e.g. `actions/setup-node@<40-hex-sha> # v5`.

Locations:

- `action.yml:49`
- `action.yml:55`
- `action.yml:60`

### github-env-injection (severity: high)

The `run:` block at line 70 of action.yml writes the env var `INPUT_FAIL_LEVEL` (sourced from `inputs.fail_level` via `env: INPUT_FAIL_LEVEL: ${{ inputs.fail_level }}`) directly to `$GITHUB_ENV` without sanitization:

```
echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"
```

An attacker-controlled value containing newlines could inject arbitrary environment variables into subsequent steps. The fix is to sanitize before writing:

```bash
safe=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')
echo "INPUT_FAIL_LEVEL=${safe}" >> "${GITHUB_ENV}"
```

Similarly, the `elif` and `else` branches write literal strings (`any`, `none`) which are safe, but the first branch is not.

Locations:

- `action.yml:72`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed all three unpinned `uses:` references by resolving their full commit SHAs: actions/setup-node@v5 → a0853c24544627f65ddf259abe73b1d18a591444, actions/cache@v4 → 0057852bfaa89a56745cba8c7296529d2fc39830, reviewdog/action-setup@v1 → d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f. Fixed the github-env-injection by sanitizing the INPUT_FAIL_LEVEL value with `printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r'` before writing it to $GITHUB_ENV, preventing attacker-controlled newlines from injecting additional environment variables into subsequent steps.

