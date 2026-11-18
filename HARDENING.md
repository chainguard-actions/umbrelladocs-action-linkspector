<!-- markdownlint-disable -->

# Hardening Report: UmbrellaDocs--action-linkspector/v1.5.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **UmbrellaDocs--action-linkspector/v1.5.5** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

A composite step writes the workflow-controlled input `inputs.fail_level` to $GITHUB_ENV without stripping newlines first. The input reaches the step through env var INPUT_FAIL_LEVEL, and the step runs `echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"`. If the value contains a newline, it can add extra environment variables such as LD_PRELOAD or NODE_OPTIONS for later steps. To fix it, run `safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')` before the write and write `$safe` instead.

Locations:

- `action.yml:84`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 080b5e2..fc5427d 100644
--- a/action.yml
+++ b/action.yml
@@ -1,7 +1,5 @@
----
 name: Run Linkspector with reviewdog
-description: Run 💀Linkspector with 🐶reviewdog on pull requests to uncover
-  broken links in your content.
+description: "Run \U0001F480Linkspector with \U0001F436reviewdog on pull requests to uncover broken links in your content."
 author: Gaurav Nelson
 inputs:
   github_token:
@@ -21,20 +19,18 @@ inputs:
     default: github-check
   filter_mode:
     description: >
-      Filtering mode for the reviewdog command
-      [added,diff_context,file,nofilter]. Default is added.
+      Filtering mode for the reviewdog command [added,diff_context,file,nofilter]. Default is added.
+
     default: added
   fail_level:
     description: >
-      Exit code for reviewdog when errors are found with severity greater
-      than or equal to the given level [none,any,info,warning,error]. Default is
-      `none`.
+      Exit code for reviewdog when errors are found with severity greater than or equal to the given level [none,any,info,warning,error]. Default is `none`.
+
     default: ""
   fail_on_error:
     description: >
-      (Deprecated) Exit code for reviewdog when errors are found
-      [true,false]. This option is ignored if you use `fail_level`. Default is
-      `false`.
+      (Deprecated) Exit code for reviewdog when errors are found [true,false]. This option is ignored if you use `fail_level`. Default is `false`.
+
     default: "false"
   reviewdog_flags:
     description: Additional reviewdog flags.
@@ -50,7 +46,24 @@ inputs:
 runs:
   using: composite
   steps:
     - uses: actions/setup-node@a0853c24544627f65ddf259abe73b1d18a591444 # v5
       with:
         node-version: 24
         package-manager-cache: false
@@ -60,13 +73,13 @@ runs:
       shell: bash
     - name: Cache reviewdog binary
       id: cache-reviewdog
       uses: actions/cache@27d5ce7f107fe9357f9df03efb73ab90386fccae # v5.0.5
       with:
         path: ~/reviewdog-bin
         key: reviewdog-${{ runner.os }}-${{ runner.arch }}-${{ steps.reviewdog-version.outputs.reviewdog_version }}
     - name: Install reviewdog
       if: steps.cache-reviewdog.outputs.cache-hit != 'true'
       uses: reviewdog/action-setup@d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f # v1
       with:
         reviewdog_version: ${{ steps.reviewdog-version.outputs.reviewdog_version }}
     - name: Copy reviewdog to cache location
@@ -81,7 +94,8 @@ runs:
       shell: bash
     - run: |
         if [ -n "${INPUT_FAIL_LEVEL}" ]; then
-            echo "INPUT_FAIL_LEVEL=${INPUT_FAIL_LEVEL}" >> "${GITHUB_ENV}"
+            safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')
+            echo "INPUT_FAIL_LEVEL=${safe}" >> "${GITHUB_ENV}"
         elif [ "${INPUT_FAIL_ON_ERROR}" = "true" ]; then
             echo "INPUT_FAIL_LEVEL=any" >> "${GITHUB_ENV}"
         else

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, prior-fix

**Notes:**

In action.yml, the step that writes INPUT_FAIL_LEVEL to $GITHUB_ENV now removes newline and carriage-return characters from the input first (safe=$(printf '%s' "$INPUT_FAIL_LEVEL" | tr -d '\n\r')) and writes the cleaned value. This is the same security change the previous harden made, so applying it fixes both findings. The previous patch's other edits (YAML description reformatting) only changed formatting, so I didn't redo them. Nothing was run to test the change.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Step 'Add cached reviewdog to PATH' in action.yml now strips newlines and carriage returns from "$HOME/reviewdog-bin" (printf | tr -d '\n\r') before writing it to "$GITHUB_PATH", which is now quoted. The PATH entry is unchanged for a normal $HOME. Nothing was run to test it.

