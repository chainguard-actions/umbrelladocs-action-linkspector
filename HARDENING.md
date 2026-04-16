# Hardening Report: umbrelladocs--action-linkspector/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `c40cfe5fa14e08549b1b988e7e5a26da4816abf0`

**Test Policy SHA:** `f2e7d85641cde4267138117189b8eba7ba2bfbde`

Action **umbrelladocs--action-linkspector/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml contains three composite action steps that reference mutable tags instead of full 40-character commit SHAs. If any of these upstream actions are compromised or their tags are moved, the action will silently execute attacker-controlled code. Failing references:
- `uses: actions/setup-node@v5` (line 52)
- `uses: actions/cache@v4` (line 61)
- `uses: reviewdog/action-setup@v1` (line 67)
Each should be pinned to a full SHA, e.g. `actions/setup-node@11bd71901bbe5b1630ceea73d27597364c9af683 # v5`.

Locations:

- `action.yml:52`
- `action.yml:61`
- `action.yml:67`

### github-env-injection (severity: high)

The run: block starting at line 80 of action.yml writes the attacker-controlled input `inputs.fail_level` to `$GITHUB_ENV` without sanitization. The value is first assigned to the env var `INPUT_FAIL_LEVEL` via `env: INPUT_FAIL_LEVEL: ${{ inputs.fail_level }}`, then written with `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`. Routing through an env variable does NOT sanitize the value — a newline embedded in `inputs.fail_level` can inject arbitrary key=value pairs into the runner's environment for subsequent steps. The required sanitization step `printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r'` is absent before the write.

Locations:

- `action.yml:82`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed all four issues in hardened/umbrelladocs--action-linkspector/v1.4.1/action.yml:
1. Pinned actions/setup-node@v5 to @11bd71901bbe5b1630ceea73d27597364c9af683 # v5 (SHA from finding description)
2. Pinned actions/cache@v4 to @5a3ec84eff668545956fd18022155c47e93e2684 # v4 (SHA from pre-commit--action hardened file)
3. Pinned reviewdog/action-setup@v1 to @d8edfce3dd5e1ec6978745e801f9c50b5ef80252 # v1 (SHA from reviewdog--action-shellcheck hardened file)
4. Fixed github-env-injection: added safe_fail_level=$(printf '%s' "${INPUT_FAIL_LEVEL}" | tr -d '\n\r') before writing to GITHUB_ENV to strip embedded newlines that could inject arbitrary environment variables. Note: lookup_action_sha tool consistently returned errors for all refs tried; SHAs were sourced from the finding description (setup-node) and from other already-hardened actions in the repository (cache, reviewdog/action-setup).

