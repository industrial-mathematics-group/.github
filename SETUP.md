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
| `workflow-templates/` | One-click versions of the three caller workflows (*Actions → New workflow*) |
| `logos/` | Optional: logo kits (CC BY 4.0) |

This repository must be **public** for the defaults and the reusable workflows to work.

## 1. Organisation settings (once)

- **Actions → General:** allow `actions/*` and the actions your packages use (julia-actions, codecov, TagBot).
- **Actions → General → Workflow permissions:** "Read and write permissions" and "Allow GitHub Actions to create and approve pull requests" (needed for CompatHelper and Documenter).
- **Actions → General → Access** of *this* repository (`.github` → Settings → Actions → General → Access): "Accessible from repositories in the organization", so other repositories may call the reusable workflows.

## 2. Per repository

1. **Add the workflows.** Go to *Actions → New workflow* and pick **AI declaration check** and **AI declaration badges** (and **AI file declarations** for repositories that declare files with aidecl) from the "By Industrial Mathematics Group" section. Or copy them by hand into `.github/workflows/` and replace `$default-branch` with `main`.
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
[![AI generation](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<Package>.jl/badges/ai-generation.json)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#g-generation)
[![Human review](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<Package>.jl/badges/ai-human-review.json)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#h-human-review)
[![AI review](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<Package>.jl/badges/ai-review.json)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#a-ai-review)
```

The three dynamic badges show "no declarations yet" until the first declared PR is merged. Run the *AI badge* workflow once by hand (*Actions → AI badge → Run workflow*) to create the `badges` branch.

## Optional: file-level declarations (aidecl)

For repositories with results, data or papers, or with casual single-commit uploads:

1. **Once for the organisation:** create the public repository `industrial-mathematics-group/aidecl` from the aidecl source and push it. The workflow installs the tool from there; tag a release (e.g. `v0.3.0`) if you want repositories to pin it.
2. **Per repository:** add `AIDECL.toml` (`aidecl init …`, `aidecl annotate …`) and the **AI file declarations** workflow (*Actions → New workflow*). It runs `aidecl lint` on every push and PR, and on pushes to `main` it publishes the badges:

```markdown
[![AI generation (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aidecl-generation.json)](https://github.com/industrial-mathematics-group/aidecl)
[![Human review (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aidecl-human-review.json)](https://github.com/industrial-mathematics-group/aidecl)
[![AI review (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aidecl-ai-review.json)](https://github.com/industrial-mathematics-group/aidecl)
```

3. Optionally require the status check **`aidecl / lint`** on `main`.

Both systems can run side by side: the PR check and trailer badges describe changes, aidecl describes files. They use different badge files on the same `badges` branch.

## Minimum levels per repository

Put a `[policy]` table into the repository's `AIDECL.toml`. You don't need any annotations for this, just `version = 3` and the policy. Both the PR check and `aidecl lint` enforce it:

```toml
version = 3

[policy]
"*" = { human = "H1" }
G3  = { human = "H2", ai = "A2" }
```

The PR check reads the policy from the **base branch**, so a pull request can't lower the bar for itself.

## Updating from the A-, A0–A3 version

The AI review scale moved up by one (A- → A0, A0 → A1, A1 → A2; A3 now means an extensive review or benchmark by a different model).

1. **Existing `AIDECL.toml` files and inline tags:** run `aidecl migrate` (aidecl 0.3.0). It keeps comments and layout, and only changes the AI review levels and the version. Previous A2 values stay A2 with a note: raise them to A3 if the review was extensive or benchmarked. G0 files with the previous A0 get a TODO, since the new scale has no self-check for human-written content. Until a repository is migrated, `aidecl lint` fails with a hint to run `migrate`.
2. **Policies:** `migrate` converts `ai = ...` in `[policy]` as well. A previous `ai = "A2"` (different model) becomes A2 (new session); change it to A3 to keep requiring a different model.
3. **Commit trailers on `main`** are not rewritten. Old `A-` trailers count as A0 in the badges. Other values from the previous scale would be read on the new one and show one level too low. To prevent that, give the badge workflow the last commit on `main` that was declared on the previous scale:

   ```yaml
   jobs:
     badge:
       uses: industrial-mathematics-group/.github/.github/workflows/ai-badge.yml@main
       with:
         previous-ai-scale-until: 0123abc   # this commit and all before it use A-, A0-A3
   ```
4. **Open pull requests** whose description uses the previous ticks or levels fail the check until they're updated to the new template. The `ai-review:A-` label is removed automatically.
5. **Labels:** the check gives the existing `ai-review:A0`–`A3` labels their new colours and descriptions the first time it uses them. Closed PRs keep their labels, which then read on the new scale. Rename the old labels first (e.g. to `old-ai-review:A1`) if you want to keep their meaning visible.

## Updating from the V0–V3 version

1. **Caller workflow:** in each repository, the AI declaration caller workflow (`.github/workflows/ai-declaration.yml`) needs `contents: read` next to its other permissions. The check reads `AIDECL.toml` from the base branch.
2. **Existing `AIDECL.toml` files and inline tags:** run `aidecl migrate`, then fill in the TODOs it reports and run `aidecl lint`.
3. **README badges:** replace them with the three new ones above. The old `ai-verification.json` and `aidecl-verification.json` files stay on the `badges` branch but are no longer updated.
4. **Old labels:** existing PR labels `verified:V*` are removed automatically the next time the check runs on that PR. You can delete the labels themselves in the repository's label settings.

## How it fits together

```
feature branch ──(anything goes)──► PR into main
                                     │  template asks for the declaration
                                     │  "declaration / check" validates it, sets ai:G* / human:H* / ai-review:A* labels
                                     ▼
                              squash merge ──► commit on main with trailers
                                                 │
                                                 ▼
                                    "AI badge" workflow ──► badges branch ──► README badges
```

- The declaration is only enforced for PRs into `main`. Commits on other branches are not checked. Draft PRs are checked too, so problems show up early, but only the state at merge counts.
- PRs opened by bots (CompatHelper, Dependabot) skip the check.
- Commits from before the policy have no trailers and are not counted in the badges.

## Forgejo mirrors

The trailers are part of the commit messages, so they are mirrored as-is. The checks and badges run on GitHub only.
