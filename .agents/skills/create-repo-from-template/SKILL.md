---
name: create-repo-from-template
description: Create a new repository from this template repository.
disable-model-invocation: true
---

# Create a repository from this template

Use this skill only when the user explicitly asks to create a repository from this template.
Run it from the root of this template repository.

## Confirm the source

1. Confirm that the current directory is the template repository root:

   ```bash
   git rev-parse --show-toplevel
   ```

2. Resolve the repository root to an absolute path.
3. Read the complete `git status --short` output.
4. Report that the skill copies the current working tree, including uncommitted changes, except for:
   - `.git/`
   - `.agents/skills/create-repo-from-template/`

If the current directory is not this repository root, stop and report the required directory.

Completion criterion: the source root is known, the source status is reported, and the exclusion list is clear.

## Gather project details

Ask for:

- The destination path as an absolute path.
- The project name.
- A one-sentence project description.
- Setup instructions for the new project, if available.
- The package manager, if it is not npm.
- Build and type-check commands, if they are not clear from the new project files.

If the user gives a description but no project name, suggest three short kebab-case names. Explain the best choice and ask the user to select one or provide another name.

Do not invent setup commands, package managers, build commands, or type-check commands.

Completion criterion: the destination and every required project value are known, or the user has confirmed that optional values are empty.

## Inspect the destination

1. Convert the destination to an absolute path.
2. Stop if the destination is the template root or is inside the template root.
3. If the destination does not exist, include directory creation in the plan.
4. If the destination exists, list its contents.
5. If the destination contains `.git`, report that it is already a Git repository and ask whether to use it. Preserve its Git metadata. Do not delete `.git` or change its current branch without explicit approval.
6. If the destination is non-empty, show the files that would conflict with template files. Ask once whether to merge and overwrite those conflicts.
7. If `.github/workflows/validate.yml.example` is in the source, include this activation in the plan:
   - Copy it to `.github/workflows/validate.yml` in the destination.
   - Remove the `.example` file after activation.
   - List either workflow path as a conflict when it already exists in the destination.

Show a local creation plan that includes:

- The source and destination paths.
- The files that will be copied.
- The files that will be excluded.
- The files that will be changed for the project name and description.
- The workflow activation and its final path.
- Any conflicts.
- Whether the skill will initialize Git or use existing Git metadata.

Wait for approval before creating or changing the destination.

Completion criterion: the destination state is known, every conflict is listed, and the user approves the local creation plan.

## Copy the template

1. Create the destination when it does not exist.
2. Copy the current template working tree, including hidden files, while excluding:
   - `.git/`
   - `.agents/skills/create-repo-from-template/`
3. Keep all other template files, including all other `.agents/skills/` files.
4. Preserve existing destination files that are not conflicts.
5. Overwrite conflicting destination files only when the user approved that action.
6. When `.github/workflows/validate.yml.example` was copied:
   - Rename it to `.github/workflows/validate.yml` when the active path does not exist.
   - Replace an existing active path only when that conflict was approved.
   - Stop before changing either path when the conflict was not approved.
7. Report the activated workflow path and confirm that the `.example` file is absent from the destination.

Use absolute paths for filesystem commands. Use a method such as `rsync` that copies hidden files and supports the exclusion list.

Completion criterion: every source file except the two exclusions exists at the destination, the approved workflow activation is complete, and approved conflicts have the approved contents.

## Customize project files

Read the copied `AGENTS.md` and `README.md` before editing them.

Update `AGENTS.md`:

- Replace the placeholder project description at the top with the supplied one-sentence description.
- Update the matching project description in `Project facts`.
- Add the supplied non-npm package manager only when one was supplied.
- Add supplied build and type-check commands only when supplied.
- Keep the file short and keep its reusable rules and links.

Update `README.md`:

- Replace `Project Template` with the project name.
- Replace the opening template description with the project description.
- Keep the progressive disclosure guidance and reusable skill descriptions that still apply.
- Remove the `create-repo-from-template` skill entry after copying.
- Remove repository-creation wording from the included skill list after copying.
- Remove the inactive workflow example entry after activating the workflow.
- Remove template-specific wording that would describe the new repository as a template.

The creation skill owns all instructions for copying and setting up a repository. Do not add those instructions to the copied README.

Do not remove project files or skills unless the user explicitly requests it.

Completion criterion: `AGENTS.md` and `README.md` contain the selected project name and description, contain no unresolved template placeholders, and preserve applicable reusable guidance.

## Review before the first commit

From the destination repository, show:

- `git status`.
- The files copied.
- The files excluded.
- The activated workflow path.
- The final project name and description.
- The complete diff for `AGENTS.md` and `README.md`.

Run the repository's defined checks only when the copied project defines them. Do not guess checks.

If the destination has no `.git` directory, wait for approval before initializing Git. Then initialize a new repository and set its default branch to `master`:

```bash
git init
git branch -M master
```

Stage the complete destination contents and inspect the staged diff. Run:

```bash
git diff --cached --check
```

Stop if the staged diff contains a likely secret, credential, private key, or unrelated file.

Wait for approval before creating the first commit.

Completion criterion: the destination diff is reviewed, applicable checks pass, the staged diff is clean, and the user approves the first commit.

## Create the local repository

Create exactly one initial commit:

```bash
git commit -m '✨ feat(repo): initialize from template'
```

Do not push during the local repository step.

Completion criterion: one initial commit exists on the `master` branch, or the existing repository state and branch are clearly reported when the user approved an existing Git repository.

## Optional GitHub setup

After the local commit, ask whether to create and push a GitHub repository with the GitHub CLI (`gh`). Keep the local repository complete when the user declines.

If the user agrees:

1. Ask for the GitHub owner, repository name, and visibility (`private`, `public`, or `internal`). Use the project name as the default repository name.
2. Confirm that `gh` is authenticated:

   ```bash
   gh auth status
   ```

   If authentication fails, report the error and stop the GitHub step. Do not run `gh auth login`.

3. Check for an existing `origin` remote. Stop and ask before changing it.
4. Check whether the selected GitHub repository already exists. Stop and report the conflict when it exists.
5. Show the exact GitHub repository and visibility. Wait for approval before creation or push.
6. From the destination repository, run the matching command:

   ```bash
   gh repo create OWNER/REPOSITORY --private --source . --remote origin --push
   gh repo create OWNER/REPOSITORY --public --source . --remote origin --push
   gh repo create OWNER/REPOSITORY --internal --source . --remote origin --push
   ```

Do not force-push or rewrite history.

Completion criterion: the GitHub repository is created and the `master` branch is pushed, or the skill reports why GitHub setup did not happen.

## Completion report

Report:

- The destination path.
- The project name.
- The activated workflow path.
- The initial commit and branch.
- The GitHub repository URL when one was created.
- Any checks that ran.
- Any skipped GitHub step and its reason.
