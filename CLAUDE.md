# CLAUDE.md

This file provides guidance to AI assistants working with this repository.

## Repository Overview

This is a **GitHub Skills educational course template** — specifically, the
[Introduction to GitHub](https://github.com/skills/introduction-to-github)
course. It is not a software application. There is no build system, no runtime
code, and no test suite. The repository's purpose is to teach beginners the
core GitHub workflow (branches, commits, pull requests, merges) through a
guided, automated four-step exercise.

## Repository Structure

```
.
├── .github/
│   ├── dependabot.yml          # Monthly auto-updates for GitHub Actions
│   ├── script/
│   │   └── STEP                # Plain-text file containing the current step number (0–4 or X)
│   └── workflows/
│       ├── 0-start.yml         # Triggered on template instantiation; advances to step 1
│       ├── 1-create-a-branch.yml   # Listens for my-first-branch creation
│       ├── 2-commit-a-file.yml     # Listens for a push to my-first-branch
│       ├── 3-open-a-pull-request.yml  # Listens for a PR opened from my-first-branch
│       └── 4-merge-your-pull-request.yml  # Listens for merge into main
├── images/                     # PNG screenshots used in README.md instructions
├── .gitignore                  # Standard OS/binary ignores; no language-specific rules
├── LICENSE                     # MIT License (copyright GitHub, Inc.)
└── README.md                   # The interactive course document (primary content)
```

## How the Course Automation Works

The automated step progression is the core mechanism of this repository.

### Step State

The file `.github/script/STEP` contains a single value representing the
learner's current step: `0`, `1`, `2`, `3`, `4`, or `X` (finished).

### Workflow Trigger Chain

Each workflow:
1. Reads the current step from `.github/script/STEP`.
2. Checks a condition — only proceeds if the step number matches what it
   expects (guarding against duplicate triggers).
3. Uses the `skills/action-update-step@v1` action to:
   - Increment `STEP` to the next value.
   - Rewrite `README.md` to close the current `<details>` block and open
     the next one, surfacing new instructions to the learner.

| Workflow file              | Trigger event              | Guards on step | Advances to |
|----------------------------|----------------------------|----------------|-------------|
| `0-start.yml`              | Push to `main` / dispatch  | `0`            | `1`         |
| `1-create-a-branch.yml`    | Branch created             | `1`, branch = `my-first-branch` | `2` |
| `2-commit-a-file.yml`      | Push to `my-first-branch`  | `2`            | `3`         |
| `3-open-a-pull-request.yml`| PR opened/reopened         | `3`, base = `main`, head = `my-first-branch` | `4` |
| `4-merge-your-pull-request.yml` | Push to `main`        | `4`            | `X`         |

### README Structure

`README.md` uses HTML `<details id=N>` elements, one per step. The currently
active step has the `open` attribute set on its `<details>` tag; all others
are closed. The `skills/action-update-step@v1` action manages this by
rewriting the file on each transition.

Do not manually reorder or restructure the `<details>` blocks — the action
matches them by `id` attribute.

## Key Conventions

- **Branch name is significant**: The workflows hard-code `my-first-branch`.
  Renaming it breaks the automation.
- **`STEP` file is the source of truth**: All workflows read from
  `.github/script/STEP`. Manual edits to this file affect course progression.
- **No programming languages**: There is no source code to build, lint, or
  test. Do not add build tooling unless fundamentally changing the repo's
  purpose.
- **Images are referenced by relative path**: All `![...](/images/...)` paths
  in `README.md` resolve from the repository root. Do not relocate files in
  `images/`.
- **MIT Licensed**: Changes must remain compatible with the MIT License.

## Development Guidelines for AI Assistants

### Modifying Course Content

- Edit `README.md` for text/instruction changes; preserve all `<details id=N>`
  tags and their `id` attributes exactly.
- Author notes are in `<!-- <<< Author notes: ... >>> -->` HTML comments.
  These are instructions for course maintainers and should not appear in
  learner-facing output.
- Images live in `images/`. Add new images there; reference them with root-
  relative paths (`/images/filename.png`).

### Modifying Workflows

- All workflows use `actions/checkout@v3` and `skills/action-update-step@v1`.
- Permissions are intentionally minimal: `contents: write` only (required to
  update `STEP` and `README.md`).
- Add guard conditions (`if:` blocks) to prevent workflows from running out
  of order or on the template repository itself (`!github.event.repository.is_template`).
- The `from_step` / `to_step` inputs to `skills/action-update-step@v1` must
  match the actual values in `.github/script/STEP`.

### Dependabot

Dependabot is configured to check for GitHub Actions version updates monthly.
When it opens PRs, review that `actions/checkout` and
`skills/action-update-step` are pinned to their latest stable versions.

### What Not to Do

- Do not add a package manager, build script, or CI test job — this repo
  intentionally has none.
- Do not change the `my-first-branch` branch name referenced in workflows
  without updating all four workflow files consistently.
- Do not remove the `<details id=N>` wrappers from `README.md`; the step-
  update action depends on them.
- Do not commit secrets or tokens; workflows use `secrets.GITHUB_TOKEN`
  (automatically provided by GitHub).

## Current State

- Current step stored in `.github/script/STEP`: check that file for the live value.
- Default branch: `main` (also referred to as `master` in some local contexts).
- Active working branch for AI contributions: `claude/add-claude-documentation-qD9IP`.
