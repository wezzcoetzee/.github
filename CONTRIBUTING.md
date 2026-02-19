# Contributing

## Setup

```bash
git clone https://github.com/wesleycoetzee/.github.git
cd .github
chmod +x .git-hooks/commit-msg
git config core.hooksPath .git-hooks
```

## Branch Naming

```
<type>/<short-description>
```

Examples: `feat/yaml-issue-forms`, `fix/stale-workflow`, `docs/contributing-guide`

## Commit Messages

This repo enforces [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>[optional scope]: <description>

[optional body]

[optional footer]
```

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`

The `commit-msg` hook will reject commits that don't match this format.

## Pull Request Process

1. Branch off `main`
2. Make your changes with conventional commits
3. Open a PR — the title must follow conventional commit format (enforced by CI)
4. PRs require at least one approval before merging
5. Squash merge into `main`

## Code Style

Formatting is enforced via Prettier. Run `prettier --write .` before committing, or configure your editor to format on save using the `.editorconfig` and `.prettierrc.json` in this repo.
