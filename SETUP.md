# Maintainer setup

This repository (`industrial-mathematics-group/.github`) holds everything that applies to the whole organisation.

| Path | Purpose |
|---|---|
| `profile/README.md` | Organisation front page on GitHub |
| `.github/CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md` | Default community files for every repository without its own |
| `.github/PULL_REQUEST_TEMPLATE.md`, `.github/ISSUE_TEMPLATE/` | Default PR and issue templates |
| `.github/workflows/ai-declaration.yml` | Reusable workflow: checks the AI declaration, sets labels |
| `.github/workflows/ai-badge.yml` | Reusable workflow: publishes the README badges |
| `.github/workflows/aidecl.yml` | Reusable workflow: file-level declarations with aidecl (lint + badges) |
| `workflow-templates/` | One-click versions of the two caller workflows (*Actions → New workflow*) |
| `logos/` | Logo kits (CC BY 4.0) |

This repository must be **public** for the defaults and the reusable workflows to work.

## 1. Organisation settings (once)

- **Actions → General:** allow `actions/*` and the actions your packages use (julia-actions, codecov, TagBot).
- **Actions → General → Workflow permissions:** "Read and write permissions" and "Allow GitHub Actions to create and approve pull requests" (needed for CompatHelper and Documenter).
- **Actions → General → Access** of *this* repository (`.github` → Settings → Actions → General → Access): "Accessible from repositories in the organization", so other repositories may call the reusable workflows.

## 2. Per repository

1. **Add the two workflows.** Go to *Actions → New workflow* and pick **AI declaration check** and **AI declaration badges** from the "By Industrial Mathematics Group" section. Or copy them by hand into `.github/workflows/` and replace `$default-branch` with `main`.
2. **Merge settings** (*Settings → General → Pull Requests*):
   - allow **squash merging** only,
   - default squash commit message: **"Pull request title and description"**. This turns the declaration block into git trailers on `main`.
3. **Protect `main`** (*Settings → Rules → Rulesets*, or *Branches → Branch protection*):
   - require a pull request before merging,
   - require the status check **`declaration / check`**.

   You don't need to require approvals; as the only maintainer you couldn't approve your own PRs.
4. **Badges in the README** (replace `<Package>`):

```markdown
[![AI policy](https://img.shields.io/badge/AI_policy-declared-9558B2.svg)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#ai-assisted-contributions)
[![AI generation](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<Package>.jl/badges/ai-generation.json)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#generation-levels-how-was-it-written)
[![Verification](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<Package>.jl/badges/ai-verification.json)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#verification-levels-how-was-it-checked)
```

The two dynamic badges show "no declarations yet" until the first declared PR is merged. Run the *AI badge* workflow once by hand (*Actions → AI badge → Run workflow*) to create the `badges` branch.

## Optional: file-level declarations (aidecl)

For repositories with results, data or papers, or with casual single-commit uploads:

1. **Once for the organisation:** create the public repository `industrial-mathematics-group/aidecl` from the aidecl source and push it. The workflow installs the tool from there; tag a release (e.g. `v0.1.0`) if you want repositories to pin it.
2. **Per repository:** add `AIDECL.toml` (`aidecl init …`, `aidecl annotate …`) and the **AI file declarations** workflow (*Actions → New workflow*). It runs `aidecl lint` on every push and PR, and on pushes to `main` it publishes the badges:

```markdown
[![AI generation (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aidecl-generation.json)](https://github.com/industrial-mathematics-group/aidecl)
[![Verification (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aidecl-verification.json)](https://github.com/industrial-mathematics-group/aidecl)
```

3. Optionally require the status check **`aidecl / lint`** on `main`.

Both systems can run side by side: the PR check and trailer badges describe changes, aidecl describes files. They use different badge files on the same `badges` branch.

## How it fits together

```
feature branch ──(anything goes)──► PR into main
                                     │  template asks for the declaration
                                     │  "declaration / check" validates it, sets ai:G* / verified:V* labels
                                     ▼
                              squash merge ──► commit on main with trailers
                                                 │
                                                 ▼
                                    "AI badge" workflow ──► badges branch ──► README badges
```

- The declaration is only enforced for PRs into `main`. Branches, forks and draft work are not checked.
- PRs opened by bots (CompatHelper, Dependabot) skip the check.
- Commits from before the policy have no trailers and are not counted in the badges.

## Forgejo mirrors

The trailers are part of the commit messages, so they are mirrored as-is. The checks and badges run on GitHub only.
