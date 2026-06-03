# Hardening Report: umbrelladocs--action-linkspector/v1.5.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **umbrelladocs--action-linkspector/v1.5.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The run: block at approximately line 80 of action.yml writes the attacker-controlled value `inputs.fail_level` to `$GITHUB_ENV` without sanitization. The input is mapped to the env var `INPUT_FAIL_LEVEL` via `env: INPUT_FAIL_LEVEL: ${{ inputs.fail_level }}`, then written directly with `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`. Routing through an env: variable does NOT sanitize the value — the required sanitization step (`printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r'`) is missing before the write. An attacker could inject newlines into `inputs.fail_level` to set arbitrary environment variables in subsequent steps.

Locations:

- `action.yml:80`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection vulnerability in action.yml at the step that writes INPUT_FAIL_LEVEL to $GITHUB_ENV. Added sanitization using `safe_fail_level=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')` before the echo write, so that attacker-controlled newlines in `inputs.fail_level` cannot inject arbitrary environment variables into subsequent steps.

