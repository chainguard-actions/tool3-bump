<!-- markdownlint-disable -->

# Hardening Report: tool3--bump/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tool3--bump/v1.5.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ steps.bump.outputs.version }} expression is directly interpolated inside a run: shell command string on line 18 of bump.yml. The value of steps.*.outputs.* is workflow-controllable and flows through YAML template substitution before the shell sees it, enabling command injection. The offending line is: run: echo "${{ steps.bump.outputs.version }}"

Locations:

- `.github/workflows/bump.yml:18`

### unpinned-uses (severity: high)

Multiple uses: references are pinned to mutable tags or branch names instead of full 40-character commit SHAs. In build.yml: 'actions/checkout@v4' (line 9) and 'actions/setup-node@v4' (line 11). In bump.yml: 'actions/checkout@master' (line 12) and 'tool3/bump@master' (line 13). These can be silently updated to malicious code without changing the ref.

Locations:

- `.github/workflows/build.yml:9`
- `.github/workflows/build.yml:11`
- `.github/workflows/bump.yml:12`
- `.github/workflows/bump.yml:13`

### missing-permissions (severity: medium)

Neither build.yml nor bump.yml defines a top-level permissions: key, and neither has job-level permissions: blocks on any of their jobs. Without explicit permissions, workflows run with the default (often write-all) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/build.yml:1`
- `.github/workflows/bump.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across build.yml and bump.yml:
1. script-injection (bump.yml line 18): Moved `${{ steps.bump.outputs.version }}` into an env: block as BUMP_VERSION and referenced it as `$BUMP_VERSION` in the shell command.
2. unpinned-uses: Pinned all four action references to full commit SHAs — actions/checkout@v4 → 11d5960a..., actions/setup-node@v4 → 49933ea5..., actions/checkout@master → 61b9e375..., tool3/bump@master → 6bba234e... — with original tags preserved in comments.
3. missing-permissions: Added `permissions: {}` top-level block to both build.yml and bump.yml to enforce least-privilege token access.

