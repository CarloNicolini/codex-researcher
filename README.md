# Codex Researcher

Codex Researcher is an autonomous pipeline that refines a Markdown manuscript into a structured, publication-ready research paper by orchestrating repeated `codex` CLI iterations. Each loop injects a disciplined persona, shared notes, and contextual prompts to expand, formalize, critique, and polish the document while keeping the project on disk as the single source of truth.

## How it works

- **Persona-driven prompts.** Every iteration begins with the “Senior Principal Researcher” persona (see `SYSTEM_CONTEXT`) to enforce LaTeX math, structured prose, and an on-disk manuscript as the single source of truth.
- **Prompt overrides.** Replace any stage prompt inline using `--prompt stage:text` or load the prompt body from a file via `--prompt-file stage:path` (stages: `init`, `expansion`, `mathematize`, `critique`, `polish`).
- **Iteration strategy.** The tool runs up to `--iterations` loops, cycling through the init, expansion, critique, mathematize, and polish stages so the manuscript grows, tightens, and is validated over time.
- **Multi-file manuscript handling.** Starting from either a single Markdown file or a directory, the script works with numbered section files (e.g., `1_introduction.md`) and never prints file contents unless explicitly requested.
- **Notes file.** `shared_task_notes.md` holds concise guidance for the next iteration, and the prompts remind Codex to read, update, or create that file.
- **LaTeX traversal.** Point `--file` at a main `.tex` file and the script discovers every `\input`/`\include` chain so the prompt can tell Codex exactly which `.tex` files comprise the project.
- **Minimal automation.** This version no longer handles git branches, backups, budgets, or PR automation—focus on the prompt loop and let your existing workflows manage version control.

## Getting started

### Requirements

- `bash` (>= 4.4) on Unix-like systems.
- `codex` CLI accessible on `PATH`.

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
- `--file` / `-f`: Sets the target Markdown file/directory or a main `.tex` file (default `project.md`). When you pass a `.tex` file, the script traces its `\input`/`\include` graph so Codex can work across every referenced file.
- `--instructions` / `-i`: Path to a text file that defines a goal or special instructions (alternative to `--goal`).
- `--iterations` / `-n`: Number of Codex iterations (default `6`).
- `--delay` / `-d`: Seconds to wait between iterations (default `2`).
- `--notes-file`: Path to the shared notes file maintained across runs (default `shared_task_notes.md`).
- `--sandbox` / `-S` & `--approval` / `-A`: Configure Codex policies (`read-only`, `workspace-write`, `danger-full-access`; `untrusted`, `on-failure`, `on-request`, `never`).
- `--model`: Pass a specific model flag to the Codex CLI (e.g., `o1`).
- `--dry-run`: Print each constructed prompt without calling Codex.
- `--prompt <stage>:<text>`: Override the inline prompt text for a stage (stages: `init`, `expansion`, `mathematize`, `critique`, `polish`).
- `--prompt-file <stage>:<path>`: Use the contents of a file as a prompt for the specified stage.

See `./researcher --help` for the full list of flags.

## Workflow details

- **Iteration prompt sequence:** 
  1. Initialization writes an outline, abstract, and introduction (`PROMPT_INIT`).
  2. Expansion prompts add theoretical depth, related sections, and practical context (`PROMPT_EXPANSION`).
  3. Every third iteration introduces mathematical rigor (`PROMPT_MATHEMATIZE`); every fourth iteration is a critical review (`PROMPT_CRITIQUE`).
  4. The final iteration performs a polish pass (`PROMPT_POLISH`).
- **Minimal automation:** The simplified loop no longer creates git branches, backups, or PRs; it simply refines the manuscript on disk and stops when the iterations complete or Codex reports an error.
- **Notes propagation:** `shared_task_notes.md` is read before each iteration to preserve context and is updated during the run so future iterations know what to tackle next.

When you run against a LaTeX entry point, the script exports the include hierarchy (main file plus every `\input`/`\include` target) into the prompt so Codex understands which `.tex` files compose the project and edits them in place.

## Project layout assumptions

- If `--file` references a directory, it should contain numbered Markdown section files (`1_introduction.md`, `2_methodology.md`, …) and the script maintains that layout.
- If you start from a single file, the tool creates a directory (named after the file without extension) and splits the content into sequential sections.
- The tool edits only the files inside the target directory; downstream tools always read from disk.
