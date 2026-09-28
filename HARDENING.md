<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable tags instead of immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if those tags are moved or compromised:
- `actions/setup-node@v5` (line 53)
- `actions/cache@v4` (line 62)
- `reviewdog/action-setup@v1` (line 68)

Each should be replaced with a full SHA pin, e.g. `actions/setup-node@<40-hex-sha> # v5`.

Locations:

- `action.yml:53`
- `action.yml:62`
- `action.yml:68`

### github-env-injection (severity: high)

In the `run:` block starting at line 81, the shell variable `INPUT_FAIL_LEVEL` — which is set from `inputs.fail_level` (an untrusted caller-controlled input) via the `env:` block — is written directly to `$GITHUB_ENV` without sanitization:

```
echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"
```

An attacker who controls `inputs.fail_level` can inject newline characters to add arbitrary key=value pairs into the runner's environment for subsequent steps. The required fix is to sanitize before writing:

```bash
safe=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')
echo "INPUT_FAIL_LEVEL=${safe}" >> "${GITHUB_ENV}"
```

Locations:

- `action.yml:83`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed all four issues in hardened/action/action.yml:
1. Pinned actions/setup-node@v5 → @a0853c24544627f65ddf259abe73b1d18a591444 # v5
2. Pinned actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830 # v4
3. Pinned reviewdog/action-setup@v1 → @d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f # v1
4. Fixed GITHUB_ENV injection: sanitized INPUT_FAIL_LEVEL with `printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r'` before writing to ${GITHUB_ENV}, preventing newline-based environment variable injection attacks.

