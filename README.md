# Project Template

A small starting point for new projects. It provides agent guidance, engineering documentation, Architecture Decision Record (ADR) support, and GitHub workflow skills.

## Included files

- `AGENTS.md` contains short, project-wide agent rules.
- `docs/engineering/` contains reusable engineering guidance.
- `docs/adr/` is the default location for project ADRs.
- `.agents/skills/` contains user-invoked skills for repository creation, ADRs, commits, pushes, and pull requests.
- `.github/pull_request_template.md` provides the pull request form for this template repository and copied projects.
- `.github/workflows/validate.yml.example` is the inactive base workflow for copied projects.

## Progressive disclosure

`AGENTS.md` is intentionally short. It points to detailed guidance instead of loading every document for every task. Read a linked document only when its task condition applies.

The main references are:

- [AGENTS.md guidelines](docs/engineering/agents-md-guidelines.md)
- [ADR template](docs/engineering/adr-template.md)
- [`write-adr` skill](.agents/skills/write-adr/SKILL.md)
- [`commit-and-push` skill](.agents/skills/commit-and-push/SKILL.md)
- [`create-pr` skill](.agents/skills/create-pr/SKILL.md)
- [`create-repo-from-template` skill](.agents/skills/create-repo-from-template/SKILL.md)

## Skills

These skills are user-invoked. They do not run Git or GitHub changes without an explicit request.

- `write-adr` creates, updates, supersedes, or reviews ADRs.
- `commit-and-push` stages, commits, and pushes all current changes. Its commit format matches the `yeet` Pi extension.
- `create-pr` uses the GitHub CLI (`gh`) to create a regular open pull request.
- `create-repo-from-template` copies and customizes this template for a new repository.
