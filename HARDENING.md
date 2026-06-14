<!-- markdownlint-disable -->

# Hardening Report: umbrelladocs--action-linkspector/v1.5.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **umbrelladocs--action-linkspector/v1.5.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.yml, a run: block writes the value of the env var INPUT_FAIL_LEVEL directly to $GITHUB_ENV without sanitization. INPUT_FAIL_LEVEL is set from `${{ inputs.fail_level }}` — a caller-controlled input. An attacker can embed newline characters in the `fail_level` input to inject arbitrary key=value pairs into the GitHub environment file, potentially overriding environment variables used by subsequent steps. The required sanitization step (`printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r'`) is absent before the write: `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`

Locations:

- `action.yml:80`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection vulnerability in action.yml at line 80. The INPUT_FAIL_LEVEL value (derived from caller-controlled input `inputs.fail_level`) was being written directly to $GITHUB_ENV without sanitization. Added a sanitization step using `safe_fail_level=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')` before writing to $GITHUB_ENV, which strips any embedded newline or carriage return characters that could be used to inject arbitrary key=value pairs into the GitHub environment file.

