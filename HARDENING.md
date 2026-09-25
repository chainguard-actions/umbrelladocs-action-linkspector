<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.5.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.5.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.yml, a `run:` block writes the value of `INPUT_FAIL_LEVEL` — sourced from `inputs.fail_level` (a caller-controlled, untrusted input) via `env: INPUT_FAIL_LEVEL: ${{ inputs.fail_level }}` — directly to `$GITHUB_ENV` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). An attacker controlling the `fail_level` input can inject newlines to set arbitrary environment variables for subsequent steps. The offending line is: `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`. The fix is to sanitize before writing: `safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r'); echo "INPUT_FAIL_LEVEL=$safe" >> "$GITHUB_ENV"`.

Locations:

- `action.yml:89`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in action.yml at line 89. The user-controlled `INPUT_FAIL_LEVEL` value (from `inputs.fail_level`) was being written directly to `$GITHUB_ENV` without sanitization. Added `safe=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')` and used `$safe` when writing to `$GITHUB_ENV`, preventing newline injection attacks that could set arbitrary environment variables for subsequent steps.

