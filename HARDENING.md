<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.5.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.5.4** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.yml, a run: block writes the value of INPUT_FAIL_LEVEL (sourced from inputs.fail_level, an attacker-controlled input) directly to $GITHUB_ENV without sanitization: `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`. The env var INPUT_FAIL_LEVEL is set from `${{ inputs.fail_level }}` in the step's env: block. A newline character embedded in the input value could inject arbitrary key=value pairs into the GitHub environment file, affecting subsequent steps. The required sanitization step (`printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r'`) is absent before the write.

Locations:

- `action.yml:69`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in action.yml at line 69. Added sanitization of INPUT_FAIL_LEVEL before writing to $GITHUB_ENV: stored the result of `printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r'` in a `safe_fail_level` variable, then used that sanitized value in the echo statement. This prevents newline injection attacks where an attacker could embed newline characters in the `fail_level` input to inject arbitrary key=value pairs into the GitHub environment file.

