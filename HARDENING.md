<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.5.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.5.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.yml, a composite action run block writes the env var `INPUT_FAIL_LEVEL` — sourced from `${{ inputs.fail_level }}` (an untrusted caller-controlled input) — directly to `$GITHUB_ENV` without sanitization. The line `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"` can be exploited by an attacker supplying a newline-containing value for `inputs.fail_level` to inject arbitrary environment variables into subsequent steps. The required sanitization step (`safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')`) is missing before the write.

Locations:

- `action.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in action.yml at line 68. Added sanitization step `safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')` before writing to $GITHUB_ENV, replacing the direct `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"` with `echo "INPUT_FAIL_LEVEL=${safe}" >> "${GITHUB_ENV}"`. This strips any embedded newlines or carriage returns from the caller-controlled `inputs.fail_level` value before it is written to the environment file, preventing environment variable injection attacks.

