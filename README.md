# Researcher

`researcher` is an autonomous manuscript-refinement loop for Markdown and LaTeX projects. It runs a sequence of research-oriented prompts against a coding agent, keeps the manuscript on disk as the source of truth, updates shared notes between iterations, and commits each successful pass to Git.

The script now supports multiple backends:

- `opencode` as the default backend
- `codex` as an optional backend
- `claude` / `claude-code` as an optional backend

## Default setup: OpenCode + OpenRouter

This repo now includes a checked-in `opencode.json` that enables the `openrouter` provider by default for OpenCode. Set your OpenRouter key in the environment:

```bash
export OPENROUTER_API_KEY="..."
```

Then install OpenCode and run the script as usual:

```bash
./researcher --goal "Formalize Spectral Graph Neural Networks" \
  --file paper.md \
  --model openrouter/anthropic/claude-sonnet-4
```

Notes:

- The backend defaults to `opencode`, so you do not need to pass `--backend opencode`.
- `opencode.json` enables only the `openrouter` provider and sets OpenCode's default primary agent to `build`.
- If you prefer to pick the model interactively in OpenCode, you can omit `--model` and configure it via `opencode` directly.

## Alternative backends

Use Codex explicitly:

```bash
./researcher --backend codex --goal "Write a survey on graph sparsification"
```

Use Claude Code explicitly:

```bash
./researcher --backend claude --goal "Tighten the related work section" \
  --model claude-sonnet-4-6
```

## Requirements

- `bash` (>= 4.4) on a Unix-like system
- Git repository with a clean worktree before each run
- One installed backend CLI:
  - `opencode` for the default path
  - `codex` for Codex runs
  - `claude` for Claude Code runs

## How it works

- Persona-driven prompts keep the agent in a strict research-paper editing mode.
- The script runs staged passes: `init`, `research`, `expansion`, `mathematize`, `critique`, `crossref`, and `polish`.
- `shared_task_notes.md` is read and updated between iterations to preserve short-term context.
- If `--file` points to a main `.tex` file, the script walks the `\input`/`\include` graph and stages those files together.
- Each successful iteration stages manuscript-related files and creates a Git commit.
- The script refuses to start if the Git worktree is dirty, which avoids silently folding unrelated edits into auto-generated commits.

## Core options

- `--goal` / `-g`: High-level research objective. Required unless `--instructions` is provided.
- `--file` / `-f`: Target Markdown file or main `.tex` file. Default: `project.md`.
- `--instructions` / `-i`: Extra instructions file, used instead of or in addition to `--goal`.
- `--backend` / `-b`: Backend CLI to use: `opencode`, `codex`, `claude`, or `claude-code`. Default: `opencode`.
- `--model`: Backend-specific model id.
- `--opencode-agent`: OpenCode primary agent to use for editing passes. Default: `build`.
- `--iterations` / `-n`: Number of iterations. Default: `7`.
- `--delay` / `-d`: Delay between iterations in seconds. Default: `2`.
- `--notes-file`: Shared notes path. Default: `shared_task_notes.md`.
- `--dry-run`: Print the assembled prompts without calling the backend.
- `--prompt <stage>:<text>`: Override a stage prompt inline.
- `--prompt-file <stage>:<path>`: Load a stage prompt from a file.

These flags remain Codex-specific and are currently only meaningful with `--backend codex`:

- `--sandbox` / `-S`
- `--approval` / `-A`

See `./researcher --help` for the full flag list.

## OpenCode configuration

The checked-in `opencode.json` is intentionally small:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "enabled_providers": ["openrouter"],
  "provider": {
    "openrouter": {
      "options": {
        "apiKey": "{env:OPENROUTER_API_KEY}"
      }
    }
  },
  "default_agent": "build"
}
```

This follows the OpenCode config model:

- `opencode.json` in the project root is a project-level config
- provider selection lives under `provider`
- OpenRouter credentials can be sourced from `OPENROUTER_API_KEY`
- `default_agent` controls the primary agent OpenCode uses when none is specified

## Workflow details

The default iteration policy is:

1. Iteration 1: `init`
2. Iteration 2: `research` when there are at least 4 total iterations
3. Multiples of 3: `mathematize`
4. Multiples of 4: `critique`
5. Penultimate iteration: `crossref` when there are at least 3 total iterations
6. Final iteration: `polish`
7. Everything else: `expansion`

At the end of the run, the script optionally asks the selected backend for a one-line summary of how the manuscript improved.
