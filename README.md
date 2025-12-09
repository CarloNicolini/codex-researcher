# Codex Researcher

Codex Researcher is an autonomous pipeline that refines a Markdown manuscript into a structured, publication-ready research paper by orchestrating repeated `codex` CLI iterations. Each loop injects a disciplined persona, shared notes, and contextual prompts to expand, formalize, critique, and polish the document while keeping the project on disk as the single source of truth.

## How it works

- **Persona-driven prompts.** Every iteration builds a “Senior Principal Researcher” prompt (see `SYSTEM_CONTEXT`) that enforces LaTeX math, multi-file sectioning, focused prose, and a notes file (`shared_task_notes.md`) that keeps successive runs aligned.
- **Iteration strategy.** The script runs up to `--iterations` loops, cycling through the init, expansion, critique, mathematize, and polish prompts so the manuscript grows, tightens, and is validated over time.
- **Multi-file manuscript handling.** If the target is a single Markdown file, it is split into numbered sections (e.g., `1_introduction.md`). The tool edits only the files within the target directory; no content is printed to stdout unless explicitly requested.
- **Safety hooks.** Each run backs up the target (in `.research_backups`), tracks diffs to stop early when nothing changes, and enforces duration/cost budgets if requested.
- **Notes file.** `shared_task_notes.md` stores concise iteration instructions and context for the next researcher, and the prompts instruct Codex to read, update, or create it every time.

## Getting started

### Requirements

- `bash` (>= 4.4) on Unix-like systems.
- `codex` CLI accessible on `PATH`.
- `git` when `--disable-commits` is not used.
- Optional: `gh` CLI for automatic PR creation/merge.

### Example

```bash
./researcher --goal "Formalize Spectral Graph Neural Networks" \
    --file paper.md \
    --iterations 6 \
    --notes-file shared_task_notes.md
```

This command runs six refinement iterations on `paper.md`, splitting it into section files if needed, and maintains shared notes for future work.

## Core options

- `--goal` / `-g`: Defines the high-level research objective (required unless `--instructions` is provided).
- `--file` / `-f`: Sets the target Markdown file or directory (defaults to `project.md`).
- `--iterations` / `-n`: Maximum number of Codex iterations before exiting (default `10`).
- `--delay` / `-d`: Seconds to wait between iterations (default `2`).
- `--notes-file`: Path to the shared notes file written each iteration (default `shared_task_notes.md`).
- `--sandbox` / `-S` & `--approval` / `-A`: Configure Codex policies (`read-only`, `workspace-write`, `danger-full-access`; `untrusted`, `on-failure`, `on-request`, `never`).
- `--max-duration`, `--max-cost`, `--price-per-1k`: Stop once the wall-clock time or estimated spend exceeds the provided budgets.
- Git helpers: `--disable-commits`, `--git-branch-prefix`, `--merge-strategy`, `--owner`, `--repo`, `--worktree`, etc. are used when automation of branches/PRs is desired.

See `./researcher --help` for the full list of flags.

## Workflow details

- **Iteration prompt sequence:** 
  1. Initialization writes an outline, abstract, and introduction (`PROMPT_INIT`).
  2. Expansion prompts add theoretical depth, related sections, and practical context (`PROMPT_EXPANSION`).
  3. Every third iteration introduces mathematical rigor (`PROMPT_MATHEMATIZE`); every fourth iteration is a critical review (`PROMPT_CRITIQUE`).
  4. The final iteration performs a polish pass (`PROMPT_POLISH`).
- **Branching & commits:** With git enabled, each iteration spawns a `researcher/iteration-*` branch, commits, pushes to `origin`, and optionally opens/merges a PR via the `gh` CLI. If no file changes occur, the branch is discarded automatically.
- **Backup & integrity:** Before each iteration the target is backed up into `.research_backups`. If the hash of the manuscript does not change for `--no-change-threshold` iterations, the loop stops early.
- **Notes propagation:** `shared_task_notes.md` is read before each iteration to preserve context and is updated during the run so downstream iterations know what to tackle next.

## Project layout assumptions

- If `--file` references a directory, it should contain numbered Markdown section files (`1_introduction.md`, `2_methodology.md`, …) and the script maintains that layout.
- If you start from a single file, the tool creates a directory (named after the file without extension) and splits the content into sequential sections.
- The tool edits only the files inside the target directory; downstream tools always read from disk.

## Next steps

1. Adjust prompts or instructions via `--instructions` if you want a specific tone or extra context.
2. Hook this script into a CI/CD workflow (e.g., GitHub Actions) to run periodic refinement passes.
3. Inspect `shared_task_notes.md` and the generated section files to guide future human revisions.
