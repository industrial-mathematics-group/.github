# Maintainer setup

This repository (`industrial-mathematics-group/.github`) holds everything that applies to the whole organisation.

| Path | Purpose |
|---|---|
| `profile/README.md` | Organisation front page on GitHub |
| `.github/CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md` | Default community files for every repository without its own |
| `.github/PULL_REQUEST_TEMPLATE.md`, `.github/ISSUE_TEMPLATE/` | Default PR and issue templates |
| `.github/workflows/ai-declaration.yml` | Reusable workflow: checks the AI declaration, sets labels |
| `.github/workflows/ai-badge.yml` | Reusable workflow: publishes the README badges |
| `.github/workflows/aidecl.yml` | Reusable workflow: file-level declarations with aisignoff (lint + badges) |
| `.github/workflows/claude.yml`, `claude/` | The @claude assistant (see [below](#the-claude-assistant)) |
| `workflow-templates/` | One-click versions of the caller workflows (*Actions → New workflow*) |
| `logos/` | Optional: logo kits (CC BY 4.0) |

This repository must be **public** for the defaults and the reusable workflows to work.

## 1. Organisation settings (once)

- **Actions → General:** allow `actions/*` and the actions your packages use (julia-actions, codecov, TagBot).
- **Actions → General → Workflow permissions:** "Read and write permissions" and "Allow GitHub Actions to create and approve pull requests" (needed for CompatHelper and Documenter).
- **Actions → General → Access** of *this* repository (`.github` → Settings → Actions → General → Access): "Accessible from repositories in the organization", so other repositories may call the reusable workflows.

## 2. Per repository

1. **Add the workflows.** Go to *Actions → New workflow* and pick **AI declaration check** and **AI declaration badges** (and **AI file declarations** for repositories that declare files with aisignoff) from the "By Industrial Mathematics Group" section. Or copy them by hand into `.github/workflows/` and replace `$default-branch` with `main`.
2. **Merge settings** (*Settings → General → Pull Requests*):
   - allow **merge commits** only, so the full history of each PR is kept,
   - default merge commit message: **"Pull request title and description"**. This puts the ticked declaration questions into the merge commit on `main`, where the badges read them. Merge on GitHub, not locally: a local merge doesn't carry the description.
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

## Optional: file-level declarations (aisignoff)

For repositories with results, data or papers, or with casual single-commit uploads:

1. **Once for the organisation:** create the public repository `industrial-mathematics-group/aisignoff` from the aisignoff source and push it. The workflow installs the tool from there; tag a release (e.g. `v0.3.0`) if you want repositories to pin it.
2. **Per repository:** add `SIGNOFF.toml` (`aisignoff init …`, `aisignoff annotate …`) and the **AI file declarations** workflow (*Actions → New workflow*). It runs `aisignoff lint` on every push and PR, and on pushes to `main` it publishes the badges:

```markdown
[![AI generation (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-generation.json)](https://github.com/industrial-mathematics-group/aisignoff)
[![Human review (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-human-review.json)](https://github.com/industrial-mathematics-group/aisignoff)
[![AI review (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-ai-review.json)](https://github.com/industrial-mathematics-group/aisignoff)
```

3. Optionally require the status check **`aidecl / lint`** on `main`.

Both systems can run side by side: the PR check and its badges describe changes, aisignoff describes files. They use different badge files on the same `badges` branch.

## The @claude assistant

Lets group members ask Claude in issues and pull requests, mainly to write `SIGNOFF.toml` declarations and
website entries without touching TOML or git. Claude runs in GitHub Actions through the official
[Claude GitHub App](https://github.com/apps/claude) and [claude-code-action](https://github.com/anthropics/claude-code-action).
It pushes a branch and posts a "Create PR" link; the person who asked opens the PR, ticks their review level and merges.

| Path | Purpose |
|---|---|
| `.github/workflows/claude.yml` | Reusable workflow: installs aisignoff, loads the org guide, runs Claude |
| `workflow-templates/claude.yml` | One-click caller workflow for each repository |
| `claude/ORG-GUIDE.md` | Claude's instructions for every repository: AI levels, aisignoff, the PR declaration |
| `claude/README.md` | Guide for group members: how to ask Claude |
| `claude/examples/website-CLAUDE.md` | Starting point for the website repository's `CLAUDE.md` |
| `.github/ISSUE_TEMPLATE/claude-*.yml` | Issue forms: declare files, add to the website, ask for something else |

### Once for the organisation

1. **Install the app.** [github.com/apps/claude](https://github.com/apps/claude) → *Install* →
   `industrial-mathematics-group` → **Only select repositories** → pick the repositories that should have
   Claude. Change the selection later under *Organisation settings → GitHub Apps → Claude → Configure*.
2. **Create an API key.** In the [Claude Console](https://platform.claude.com), create a workspace (e.g.
   `github-actions`) with a monthly spend limit, then an API key in that workspace.
   A `CLAUDE_CODE_OAUTH_TOKEN` (`claude setup-token`, Pro/Max plan) works too, but bills one person's
   subscription for the whole group.
3. **Store it as an organisation secret.** *Organisation settings → Secrets and variables → Actions →
   New organization secret*: name `ANTHROPIC_API_KEY`, repository access **Selected repositories** (the same ones
   as in step 1).
4. **Allow the action.** If *Actions → General → Policies* restricts actions, add `anthropics/claude-code-action@*`.
   The reusable workflow also needs this repository's *Access* setting from section 1 (already set for the
   other reusable workflows).
5. **Members need write access.** Claude ignores anyone without write access to the repository. Give the group
   a team (e.g. `members`) with the *Write* role on the repositories with Claude.
6. **Make the guide known.** Link [`claude/README.md`](claude/README.md) from the organisation profile or tell the
   group about it.

### Per repository

1. *Actions → New workflow* → **Claude assistant (@claude)** from the "By Industrial Mathematics Group" section →
   *Configure* → *Commit changes*. Uncomment `node-version: "22"` for the website.
2. Optional: a `CLAUDE.md` in the repository root with what Claude should know about it (folder layout, what the
   files are, special declaration defaults). For the website, start from `claude/examples/website-CLAUDE.md`.
3. Optional: labels `ai-declaration`, `website` and `claude-request`, which the issue forms apply if they exist.
4. Test it: open an issue with the form **Claude: ask for something else** and ask
   "Summarise what this repository contains". A progress comment should appear within a minute or two.

The issue forms are organisation defaults: they appear in every repository without its own `ISSUE_TEMPLATE`
folder. In repositories without the Claude workflow nothing happens when they are used. A repository with its
own templates can copy the `claude-*.yml` forms it wants.

### Settings you may want to change

In the caller workflow (`with:`):

| Input | Default | Use |
|---|---|---|
| `model` / `model-name` | `claude-sonnet-5-5` / `Claude Sonnet 5.5 (Anthropic)` | A stronger model, e.g. `claude-opus-5-5` / `Claude Opus 5.5 (Anthropic)`. Change both together: `model-name` is what Claude writes into declarations. |
| `node-version` | none | `"22"` for repositories that build with Node (the website) |
| `max-turns` | `40` | Cost guard per run |
| `aidecl-ref`, `guide-ref` | `main` | Pin aisignoff or the guide to a tag |

Organisation-wide behaviour (levels, wording, PR declaration) is changed in `claude/ORG-GUIDE.md`; every
repository picks it up on its next run.

### How a request runs

```
issue form / "@claude" comment
   │  caller workflow in the repository (skipped unless "@claude" is in the text)
   ▼
reusable workflow: checkout ─► pip install aisignoff ─► ORG-GUIDE.md as ~/.claude/CLAUDE.md
   ▼
Claude (as the Claude app): reads issue + repo CLAUDE.md, runs aisignoff annotate / lint or edits files,
pushes branch claude/issue-<n>-…, posts "Create PR" link with the PR template pre-ticked (G3, model, A1)
   ▼
person opens PR ─► ticks H0–H3 and "I take responsibility" ─► declaration / check green ─► merge
```

### Costs and safety

- Each run costs a few cents to a few dollars, depending on model and task; the Console shows usage per
  workspace. `max-turns` and the 30-minute timeout cap a single run.
- Claude only acts for users with write access, cannot approve or merge PRs, and never pushes to `main`.
  On PRs, the action reads `CLAUDE.md` and `.claude/` from the base branch, so a PR can't change Claude's rules.
- Issues in public repositories are public. Point people to private repositories for unpublished work.
- Later, you can replace the API key with [Workload Identity Federation](https://github.com/anthropics/claude-code-action/blob/main/docs/setup.md#workload-identity-federation)
  (no stored secret), or use your own GitHub App for a custom bot name
  ([docs](https://github.com/anthropics/claude-code-action/blob/main/docs/setup.md#using-a-custom-github-app)).

## Minimum levels per repository

Put a `[policy]` table into the repository's `SIGNOFF.toml`. You don't need any annotations for this, just `version = 3` and the policy. Both the PR check and `aisignoff lint` enforce it:

```toml
version = 3

[policy]
"*" = { human = "H1" }
G3  = { human = "H2", ai = "A2" }
```

The PR check reads the policy from the **base branch**, so a pull request can't lower the bar for itself.

## Updating from the trailer lines to the ticked declaration

The PR template no longer ends in trailer lines (`AI-Generation:`, `Reviewed-by:`, ...): the check derives everything from the ticked questions.

1. **Merge settings** of each repository: switch to merge commits with the message "Pull request title and description" (section 2 above). With squash merges and that message, the ticks still reach `main`, but the branch history doesn't.
2. **Repositories with their own PR template** (`.github/pull_request_template.md`): copy the new "AI declaration" section into it. The others use this repository's template.
3. **Open pull requests** fail the check until their description has the new "AI declaration" section and no trailer lines.
4. **History:** commits declared with trailers keep counting in the badges; nothing needs to be rewritten.

## Updating from aidecl to aisignoff

aidecl was renamed to aisignoff (0.4.0, also on PyPI: `pip install aisignoff`), and `AIDECL.toml` to `SIGNOFF.toml`. Keys, levels and inline tags are unchanged.

1. **Nothing breaks right away:** the workflows install aisignoff, which still reads `AIDECL.toml` (with a warning), and they keep publishing the badges under the old names `aidecl-*.json` as well. Repositories that pin `aidecl-ref` to a tag from before the rename keep working too.
2. **Per repository, when convenient:** run `aisignoff migrate` (it renames `AIDECL.toml` to `SIGNOFF.toml`), commit both sides of the rename, and switch the README badges to `aisignoff-generation.json`, `aisignoff-human-review.json` and `aisignoff-ai-review.json` (see above).
3. **On your machine:** `pipx uninstall aidecl && pipx install aisignoff`.

## Updating from the A-, A0–A3 version

The AI review scale moved up by one (A- → A0, A0 → A1, A1 → A2; A3 now means an extensive review or benchmark by a different model).

1. **Existing `SIGNOFF.toml` files and inline tags:** run `aisignoff migrate` (aidecl 0.3.0 or later; aidecl is now called aisignoff). It keeps comments and layout, and only changes the AI review levels and the version. Previous A2 values stay A2 with a note: raise them to A3 if the review was extensive or benchmarked. G0 files with the previous A0 get a TODO, since the new scale has no self-check for human-written content. Until a repository is migrated, `aisignoff lint` fails with a hint to run `migrate`.
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

1. **Caller workflow:** in each repository, the AI declaration caller workflow (`.github/workflows/ai-declaration.yml`) needs `contents: read` next to its other permissions. The check reads `SIGNOFF.toml` from the base branch.
2. **Existing `SIGNOFF.toml` files and inline tags:** run `aisignoff migrate`, then fill in the TODOs it reports and run `aisignoff lint`.
3. **README badges:** replace them with the three new ones above. The old `ai-verification.json` and `aidecl-verification.json` files stay on the `badges` branch but are no longer updated.
4. **Old labels:** existing PR labels `verified:V*` are removed automatically the next time the check runs on that PR. You can delete the labels themselves in the repository's label settings.

## How it fits together

```
feature branch ──(anything goes)──► PR into main
                                     │  template asks for the declaration
                                     │  "declaration / check" validates it, sets ai:G* / human:H* / ai-review:A* labels
                                     ▼
                              merge commit ──► "title and description" on main, with the ticks
                                                 │
                                                 ▼
                                    "AI badge" workflow ──► badges branch ──► README badges
```

- The declaration is only enforced for PRs into `main`. Commits on other branches are not checked. Draft PRs are checked too, so problems show up early, but only the state at merge counts.
- PRs opened by dependency update bots (Dependabot, and CompatHelper as `github-actions[bot]`) skip the check. Other bots and AI agents don't.
- Commits from before the policy have no declaration and are not counted in the badges. Commits declared with the trailer lines of earlier templates still count.

## Forgejo mirrors

The declarations are part of the merge commit messages, so they are mirrored as-is. The checks and badges run on GitHub only.
