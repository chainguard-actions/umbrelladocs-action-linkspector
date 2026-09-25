<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if those tags are moved or compromised:
- `uses: actions/setup-node@v5`
- `uses: actions/cache@v4`
- `uses: reviewdog/action-setup@v1`
Each should be replaced with a full SHA pin, e.g. `actions/setup-node@<40-hex-sha> # v5`.

Locations:

- `action.yml:43`
- `action.yml:50`
- `action.yml:56`

### github-env-injection (severity: high)

A `run:` block writes the value of `INPUT_FAIL_LEVEL` to `$GITHUB_ENV` without sanitization. `INPUT_FAIL_LEVEL` is populated from `${{ inputs.fail_level }}` (an untrusted caller-controlled input) via the step's `env:` block. Writing it directly with `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"` allows an attacker to inject newlines and arbitrary key=value pairs into the GitHub environment, potentially overriding sensitive environment variables for subsequent steps. The fix is to sanitize before writing: `safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')` and then `echo "INPUT_FAIL_LEVEL=$safe" >> "$GITHUB_ENV"`.

Locations:

- `action.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed three unpinned `uses:` references by replacing mutable version tags with full 40-character commit SHAs (actions/setup-node@v5→a0853c24..., actions/cache@v4→0057852b..., reviewdog/action-setup@v1→d8a7baab...). Fixed github-env-injection by sanitizing INPUT_FAIL_LEVEL with `printf '%s' | tr -d '\n\r'` before writing to $GITHUB_ENV, preventing newline-based environment variable injection.

