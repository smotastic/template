# Project Template

A small starting point for new projects. It provides agent guidance, engineering documentation, Architecture Decision Record (ADR) support, and GitHub workflow skills.

## Included files

- `AGENTS.md` contains short, project-wide agent rules.
- `docs/engineering/` contains reusable engineering guidance.
- `docs/adr/` is the default location for project ADRs.
- `.agents/skills/` contains user-invoked skills for ADRs, commits, pushes, and pull requests.

## Start a new project

1. Copy this repository into the new project.
2. Replace the project description and placeholders in `AGENTS.md`.
3. Update this README with the project name and setup instructions.
4. Add project-specific commands only when the agent cannot find them in the project files.
5. Remove guidance or skills that the project does not need.

## Progressive disclosure

`AGENTS.md` is intentionally short. It points to detailed guidance instead of loading every document for every task. Read a linked document only when its task condition applies.

The main references are:

- [AGENTS.md guidelines](docs/engineering/agents-md-guidelines.md)
- [ADR template](docs/engineering/adr-template.md)
- [`write-adr` skill](.agents/skills/write-adr/SKILL.md)
- [`commit-and-push` skill](.agents/skills/commit-and-push/SKILL.md)
- [`create-pr` skill](.agents/skills/create-pr/SKILL.md)

## Skills

These skills are user-invoked. They do not run Git or GitHub changes without an explicit request.

- `write-adr` creates, updates, supersedes, or reviews ADRs.
- `commit-and-push` stages, commits, and pushes all current changes. Its commit format matches the `yeet` Pi extension.
- `create-pr` uses the GitHub CLI (`gh`) to create a regular open pull request.
