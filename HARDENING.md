<!-- markdownlint-disable -->

# Hardening Report: umbrelladocs--action-linkspector/v1.5.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **umbrelladocs--action-linkspector/v1.5.4** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.yml, the composite step that resolves the fail_level input writes the unsanitized value of INPUT_FAIL_LEVEL (sourced from inputs.fail_level via the env: block) directly to $GITHUB_ENV without first stripping newlines with `printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r'`. An attacker-controlled value containing a newline can inject arbitrary key=value pairs into the runner's environment for subsequent steps. The offending line is: `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`

Locations:

- `action.yml:79`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection vulnerability in hardened/action/action.yml at line 79. The unsanitized INPUT_FAIL_LEVEL value was being written directly to $GITHUB_ENV. The fix introduces a sanitization step using `safe_fail_level=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')` before writing to GITHUB_ENV, which strips any embedded newlines or carriage returns that could be used to inject arbitrary environment variables.

