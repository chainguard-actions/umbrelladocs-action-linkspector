<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable tags rather than immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved:
- `uses: actions/setup-node@v5` (line 53)
- `uses: actions/cache@v4` (line 63)
- `uses: reviewdog/action-setup@v1` (line 70)
Each should be replaced with a full SHA commit pin, e.g. `actions/setup-node@<40-hex-sha> # v5`.

Locations:

- `action.yml:53`
- `action.yml:63`
- `action.yml:70`

### github-env-injection (severity: high)

The `run:` block that conditionally sets `INPUT_FAIL_LEVEL` writes a user-controlled value to `$GITHUB_ENV` without sanitization. `INPUT_FAIL_LEVEL` is sourced from `inputs.fail_level` (an action input, i.e. attacker-controlled) via the `env:` block and then written as:

  echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"

A newline character embedded in `inputs.fail_level` would allow an attacker to inject arbitrary key=value pairs into the runner's environment. The required sanitization step (`safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')`) is missing before the write.

Locations:

- `action.yml:81`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed three unpinned `uses:` references in action.yml by replacing mutable tags with full 40-character SHA digests (actions/setup-node@v5→a0853c24..., actions/cache@v4→0057852b..., reviewdog/action-setup@v1→d8a7baab...). Fixed the github-env-injection finding by sanitizing the user-controlled INPUT_FAIL_LEVEL value with `printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r'` before writing it to $GITHUB_ENV, preventing newline injection attacks.

