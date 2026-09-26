<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable tags rather than immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if the upstream tag is moved or the repository is compromised. Failing references:
- `actions/setup-node@v5` (line 51)
- `actions/cache@v4` (line 57)
- `reviewdog/action-setup@v1` (line 63)

Each should be replaced with a full SHA pin, e.g. `actions/setup-node@<40-hex-sha> # v5`.

Locations:

- `action.yml:51`
- `action.yml:57`
- `action.yml:63`

### github-env-injection (severity: high)

The `run:` block at line 72 of action.yml writes the env var `INPUT_FAIL_LEVEL` (sourced from `inputs.fail_level`, a caller-controlled value) directly to `$GITHUB_ENV` without sanitization:

```
echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"
```

An attacker who controls the `fail_level` input can embed a newline character in the value, injecting arbitrary key=value pairs into the runner's environment for subsequent steps. The required fix is to sanitize the value before writing:

```bash
safe=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')
echo "INPUT_FAIL_LEVEL=${safe}" >> "${GITHUB_ENV}"
```

The same pattern applies to the `INPUT_FAIL_ON_ERROR` variable used in the same block.

Locations:

- `action.yml:72`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. Pinned all three unpinned `uses:` references to full 40-character commit SHAs: actions/setup-node@v5 → a0853c24544627f65ddf259abe73b1d18a591444, actions/cache@v4 → 0057852bfaa89a56745cba8c7296529d2fc39830, reviewdog/action-setup@v1 → d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f. Original tags preserved as inline comments. 2. Fixed github-env-injection by sanitizing INPUT_FAIL_LEVEL with `printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r'` before writing to GITHUB_ENV. The other branches write only hardcoded literal strings ('any', 'none') and were already safe.

