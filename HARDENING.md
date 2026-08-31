<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.5.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.5.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.yml, the composite step that conditionally sets INPUT_FAIL_LEVEL writes the value of the `INPUT_FAIL_LEVEL` env var directly to `$GITHUB_ENV` without sanitization: `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`. The env var is populated from `${{ inputs.fail_level }}` (untrusted caller-controlled input). An attacker can supply a value containing newline characters to inject arbitrary key=value pairs into the GitHub environment file, potentially overwriting sensitive environment variables for subsequent steps. The required sanitization (`safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')`) is absent.

Locations:

- `action.yml:80`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection vulnerability in action.yml at line 80. The INPUT_FAIL_LEVEL value (populated from caller-controlled input `inputs.fail_level`) was being written directly to $GITHUB_ENV without sanitization. Added sanitization using `safe=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r')` before writing to $GITHUB_ENV, which strips newline and carriage return characters that could be used to inject arbitrary key=value pairs into the GitHub environment file.

