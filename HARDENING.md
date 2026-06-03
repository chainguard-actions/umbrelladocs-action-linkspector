# Hardening Report: umbrelladocs--action-linkspector/v1.5.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **umbrelladocs--action-linkspector/v1.5.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.yml, a run: step writes the attacker-controlled input `inputs.fail_level` (mapped to env var INPUT_FAIL_LEVEL) directly to $GITHUB_ENV without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). An attacker can supply a newline-containing value for `fail_level` to inject arbitrary key=value pairs into the runner's environment, potentially overwriting sensitive variables like GITHUB_TOKEN or PATH. The unsanitized write is: `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`.

Locations:

- `action.yml:62`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection vulnerability in action.yml at the step that writes INPUT_FAIL_LEVEL to $GITHUB_ENV. The fix adds a sanitization step using `printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r'` to strip newlines and carriage returns from the attacker-controlled input before writing it to the environment file. The sanitized value is stored in `safe_fail_level` and that variable is used in the echo command instead of the raw input.

