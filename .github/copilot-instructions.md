<!-- .github/copilot-instructions.md - guidance for AI coding agents -->
# Repo-specific Copilot instructions

This repository is primarily a collection of GitHub Actions workflow examples under `.github/workflows/`.
These notes capture the minimal, actionable knowledge an AI coding agent needs to be productive here.

## Big picture
- Purpose: example and demonstration workflows for GitHub Actions (no application source code present).
- Major components: each YAML in `.github/workflows/` is a self-contained workflow. The agent should treat them as independent pipelines that demonstrate patterns (manual checkout, job dependencies, platform differences, action usage).

## Key files and what they show
- `.github/workflows/checkout.yaml` — shows a manual repository checkout using `git init` + `git fetch` + `git checkout main` with `GITHUB_ACTOR` and `secrets.GITHUB_TOKEN`. Important: this shows how the repo uses the token to authenticate fetches instead of `actions/checkout`.
- `.github/workflows/workflow.yaml` — simple echo steps and Node/npm version checks; demonstrates parallel jobs and job `needs` dependencies.
- `.github/workflows/workflow-commands.yaml` — demonstrates GitHub Actions workflow commands: `::error::`, `::warning::`, `::debug::`, `::notice::`, `::group::` / `::endgroup::`, and `::add-mask::` for masking secrets.
- `.github/workflows/simple-action.yaml` — example that `uses: actions/hello-world-javascript-action@v1` and reads an `outputs` value as `${{ steps.greet.outputs.time }}`.
- `.github/workflows/working-dir-shell.yml` — shows `defaults.run.shell: bash`, demonstrates cross-platform runner examples (ubuntu and windows) and environment variables like `GITHUB_SHA`, `GITHUB_WORKSPACE`.

## Discoverable patterns and conventions (do not invent)
- Workflows use explicit `runs-on:` values (`ubuntu-latest`, `windows-latest`). Keep platform semantics in mind when modifying steps.
- Job composition uses `needs:` to express ordering (see `workflow.yaml` parallel-jobs and independent-jobs examples).
- Secrets and env use: `secrets.GITHUB_TOKEN` and runner-provided env vars (`GITHUB_ACTOR`, `GITHUB_REPOSITORY`, `GITHUB_WORKSPACE`, `GITHUB_SHA`).
- Manual checkout pattern: some workflows authenticate via `https://${GITHUB_ACTOR}:${{ secrets.GITHUB_TOKEN }}@github.com/${GITHUB_REPOSITORY}.git` — if you change this, ensure branch names (e.g., `main`) and token usage remain valid.
- Action outputs: read outputs with the `steps.<id>.outputs.<name>` expression (example: `${{ steps.greet.outputs.time }}`).
- Workflow commands: the repo intentionally demonstrates the `::command::` syntax; preserve formatting when editing (e.g., `::error file=...,line=...::message`).

## Integration points / external deps
- GitHub Actions marketplace actions (example: `actions/hello-world-javascript-action@v1`).
- The workflows rely on runner-provided tooling (git, node, npm) — no pinned docker images or additional external services are referenced in the repo.

## Developer workflows / quick checks
- There are no tests or build scripts in this repository. Typical manual checks an engineer/agent can run locally:
  - Validate YAML syntax with a YAML linter (e.g., `yamllint`) or `python -c 'import yaml,sys;yaml.safe_load(sys.stdin.read())' < file.yml`.
  - Inspect workflow semantics by opening the workflow file and looking at `jobs` / `steps` / `runs-on` / `uses`.
  - To emulate runs locally, developers often use tools like `act` (not present in repo) — only use if the environment supports it.

## Editing guidance for agents
- Be conservative: these files are mostly examples. Preserve intent and examples unless the user asks to generalize.
- When adding workflows that interact with the repo, prefer `actions/checkout@v3` unless the user explicitly intends to demonstrate manual `git` operations.
- Keep `runs-on` and platform-specific shell syntax consistent (use `bash` blocks for linux/mac and `powershell`/cmd for windows steps).
- When changing authenticated git commands, ensure you do not leak secrets; the repo uses `secrets.GITHUB_TOKEN` and `::add-mask::` is shown as an example to mask values in logs.

## Quick examples (use as patterns)
- Read an action output: `${{ steps.greet.outputs.time }}` (from `simple-action.yaml`).
- Emit a build error to the Actions UI: `echo "::error::message"` (see `workflow-commands.yaml`).
- Manual checkout with token (from `checkout.yaml`): authenticate with `https://${GITHUB_ACTOR}:${{ secrets.GITHUB_TOKEN }}@github.com/${GITHUB_REPOSITORY}.git` then `git fetch` and `git checkout main`.

## What I couldn't find / assumptions made
- No application source code, tests, or CI scripts other than example workflows — treat this repository as a documentation/example repo for GitHub Actions.
- No project-specific linter, package manager, or build tool configuration was found.

If you'd like, I can:
- Run a repo scan to detect any YAML syntax issues or missing branch references.
- Convert the manual checkout example to `actions/checkout@v3` and add a comment explaining differences.

Please review these instructions and tell me which sections you want expanded or corrected.
