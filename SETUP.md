# Maintainer setup

This repository (`industrial-mathematics-group/.github`) holds everything that applies to the whole organisation.

| Path | Purpose |
|---|---|
| `profile/README.md` | Organisation front page on GitHub |
| `.github/CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md` | Default community files for every repository without its own |
| `.github/PULL_REQUEST_TEMPLATE.md`, `.github/ISSUE_TEMPLATE/` | Default PR and issue templates |
| `.github/workflows/pr-signoff.yml` | Reusable workflow *PR-signoff*: checks the AI declaration of a PR with aisignoff, sets labels |
| `.github/workflows/signoff-badges.yml` | Reusable workflow *Signoff-Badges*: publishes the README badges of the PR declarations |
| `.github/workflows/file-signoff.yml` | Reusable workflow *File-signoff*: file-level declarations with aisignoff (lint + badges) |
| `.github/ISSUE_TEMPLATE/signoff-request.yml` | The issue form *Signoff: declare or attest files*, which IMGAgent turns into a `SIGNOFF.toml` pull request |
| `.github/workflows/zensical-docs.yml` | Reusable workflow *Zensical docs*: builds `docs/` with Zensical and publishes it on GitHub Pages (see [below](#documentation-sites-zensical)) |
| `.github/workflows/pr-signoff-check.yml`, `file-signoff-check.yml`, `SIGNOFF.toml` | This repository's own PR-signoff and File-signoff checks, with the reusable workflows above |
| `workflow-templates/` | One-click versions of the caller workflows (*Actions → New workflow*) |
| `logos/` | Optional: logo kits (CC BY 4.0) |

This repository must be **public** for the defaults and the reusable workflows to work.

## 1. Organisation settings (once)

- **Actions → General:** allow `actions/*` and the actions your packages use (julia-actions, codecov, TagBot).
- **Actions → General → Workflow permissions:** "Read and write permissions" and "Allow GitHub Actions to create and approve pull requests" (needed for CompatHelper and Documenter).
- **Actions → General → Access** of *this* repository (`.github` → Settings → Actions → General → Access): "Accessible from repositories in the organization", so other repositories may call the reusable workflows.

## 2. Per repository

1. **Add the workflows.** Go to *Actions → New workflow* and pick **PR-signoff** and **Signoff-Badges** (and **File-signoff** for repositories that declare files with aisignoff) from the "By Industrial Mathematics Group" section. Or copy them by hand into `.github/workflows/` and replace `$default-branch` with `main`.
2. **Merge settings** (*Settings → General → Pull Requests*):
   - allow **merge commits** only, so the full history of each PR is kept,
   - default merge commit message: **"Pull request title and description"**. This puts the ticked declaration questions into the merge commit on `main`, where the badges read them. Merge on GitHub, not locally: a local merge doesn't carry the description.
3. **Protect `main`** (*Settings → Rules → Rulesets*, or *Branches → Branch protection*):
   - require a pull request before merging,
   - require the status check **`pr-signoff / check`**.

   You don't need to require approvals; as the only maintainer you couldn't approve your own PRs.
4. **Badges in the README** (replace `<Package>`):

```markdown
[![AI policy](https://img.shields.io/badge/AI_policy-declared-9558B2.svg)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#ai-assisted-contributions)
[![AI generation](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<Package>.jl/badges/ai-generation.json)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#g-generation)
[![Human review](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<Package>.jl/badges/ai-human-review.json)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#h-human-review)
[![AI review](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<Package>.jl/badges/ai-review.json)](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#a-ai-review)
```

The three dynamic badges show "no declarations yet" until the first declared PR is merged. Run the *Signoff-Badges* workflow once by hand (*Actions → Signoff-Badges → Run workflow*) to create the `badges` branch.

## What PR-signoff checks

*PR-signoff* is aisignoff's check of a pull request before the merge (`aisignoff lint --declare-from`, pinned by
the input `aisignoff-version`): one parser and one set of rules for the PR template, the merge commit and
`SIGNOFF.toml`.

- **Every repository:** the ticked AI declaration must be complete on its own: the levels, the models (G1–G3),
  the reviewing model (A1–A3) and the responsible human (the author, or `Responsible person: @login` for PRs
  opened by bots). In a repository without `SIGNOFF.toml`, that's all it checks.
- **With `SIGNOFF.toml`:** also every file, and with `[history]` every line, of GitHub's merge preview, with the
  `[policy]` of the base branch, so a PR can't lower the bar for itself. A PR that changes `[policy]`, `[review]`, `version`,
  `exclude` or `[history]`, the entries of `.git-blame-ignore-revs` (or removes `SIGNOFF.toml`) fails, also for maintainers: they commit such changes to `main`
  directly (with `undeclared = "error"`, give the commit message "Key: value" lines, such as `AI-Generation: G0`).
  A repository can instead let PR authors with some permissions make them, with the input `allow-policy-change`
  (for example `admin,maintain`; it needs aisignoff >= 1.0.0rc4): their PR is then still checked under the base
  branch's `[policy]`, and the job summary says that it changes the rules. The permission is the PR author's, so
  anyone who can push to that PR's branch can add such a change.
  With this, *File-signoff* on pull requests checks the same files again; keep it for pushes, merge queues and
  badges.
- The repository callers don't change: the check is still `pr-signoff / check`, triggered by
  `pull_request_target` (also on `edited`), and still sets the `ai:G*`, `human:H*` and `ai-review:A*` labels.

## Optional: file-level declarations (aisignoff)

For repositories with results, data or papers, or with casual single-commit uploads:

1. **Once for the organisation:** the workflow installs aisignoff from PyPI, pinned by the input `aisignoff-version` of `.github/workflows/file-signoff.yml`. Raise the default there to update every repository at once, after checking the aisignoff changelog for **Breaking** changes; a repository can set `aisignoff-version` itself to differ.
2. **Per repository:** add the **File-signoff** workflow (*Actions → New workflow*), and `SIGNOFF.toml` (`aisignoff init …`, `aisignoff annotate …`) or let the workflow set it up (below). It runs `aisignoff lint` on every push, PR and merge queue run, and on pushes to `main` it publishes the badges:

```markdown
[![AI generation (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-generation.json)](https://github.com/industrial-mathematics-group/aisignoff)
[![Human review (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-human-review.json)](https://github.com/industrial-mathematics-group/aisignoff)
[![AI review (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-ai-review.json)](https://github.com/industrial-mathematics-group/aisignoff)
```

   **Without `SIGNOFF.toml`**, *File-signoff* skips the check with a warning, and on a push to `main` (or a manual
   run) it opens a pull request from the branch `aisignoff-setup` that adds a `SIGNOFF.toml` with the organisation's
   defaults: `[review]` `recheck = "major"`, `[history]` on, and a `**` annotation with every level and
   `Reviewed-by` set to `TODO`. Someone fills in the `TODO`s (and annotates folders that differ), ticks a human
   review box in the description and merges; *File-signoff* fails on that pull request until no `TODO` is left. The
   pull request names the person whose push or run started the workflow as `Responsible person`. Checks start on the
   first push to the branch, since GitHub doesn't run workflows for pull requests opened with the workflow token.
   While the branch exists (also after the pull request is closed), no new one is opened: delete the branch to get
   another. It needs `pull-requests: write` in the caller (in the template) and *Settings → Actions → General →
   Allow GitHub Actions to create and approve pull requests* for the organisation or repository; without them, the
   workflow pushes the branch and its job summary links to a new pull request with the description to paste.
   Inputs: `setup: false` turns it off, `setup-recheck` (`""` leaves `[review]` out) and `setup-history` change the
   defaults.

   **The report on GitHub Pages (optional).** With `pages: true`, *File-signoff* also publishes the interactive
   folder report on every push to `main` (and manual run), at `https://industrial-mathematics-group.github.io/<repo>/aisignoff/`
   (input `pages-path`, default `/aisignoff/`; `/` puts it at the root), with a redirect to it at the root of the
   site. It needs *Settings → Pages → Source: GitHub Actions* and, in the caller, `pages: write` and
   `id-token: write` (commented out in the template). A deployment replaces the whole Pages site: in a repository
   whose Pages site is its documentation, leave it off and put the report into the documentation instead. The
   report shows the text of every file, like a public repository; Pages of a private repository need a paid plan.
3. Optionally require the status check **`file-signoff / lint`** on `main`.
4. Optionally install the IMGAgent App on the repository for the signoff form and `@IMGAgent signoff`, see
   [below](#signoff-requests-the-form-and-imgagent).

Both systems can run side by side: the PR check and its badges describe changes, aisignoff describes files. They use different badge files on the same `badges` branch.

### Signoff requests: the form and @IMGAgent

Group members can declare or attest files without TOML or git: with the issue form **Signoff: declare or attest
files** (an organisation default, `.github/ISSUE_TEMPLATE/signoff-request.yml`), or by writing
`@IMGAgent signoff` and, in their own words, what to record. The organisation's bot **IMGAgent** (a GitHub App,
its own repository `IMGAgent/imgagent`) turns either into a pull request that changes `SIGNOFF.toml` with
`aisignoff annotate` / `attest`, checked by `aisignoff lint`; the requester reviews, ticks and merges it.

- **Per repository:** install the IMGAgent App on it (*Organisation settings → GitHub Apps → IMGAgent*); it needs
  a `SIGNOFF.toml`. No workflow file. Setup, permissions and settings: IMGAgent's README.
- The pull requests follow the PR template ("Responsible person: @requester", G0 for the form, G1 and the model for
  `@IMGAgent signoff`), so *PR-signoff* checks them; as an App's pull requests, they start the checks as usual.
- Repositories with their own `.github/ISSUE_TEMPLATE/` folder don't see the organisation's forms: copy
  `signoff-request.yml` into it. Optionally create a label `signoff-request`, which the form applies if it exists.
- IMGAgent also writes documentation: `@IMGAgent docs make`, `docs update` and `docs ask` (see
  [Documentation sites](#documentation-sites-zensical)).

## Documentation sites (Zensical)

Repositories with a documentation site in `docs/` and a `zensical.toml` ([Zensical](https://zensical.org)) build
and publish it with the reusable workflow *Zensical docs*:

1. **Per repository:** *Actions → New workflow* → **Zensical docs**, and *Settings → Pages → Source:*
   **GitHub Actions**.
2. Pull requests that touch `docs/` or `zensical.toml` build the site, so a broken page or config fails before the
   merge (require the check **`docs / build`** on `main` if you like). Pushes to `main` and manual runs build and
   deploy it to `https://industrial-mathematics-group.github.io/<repo>/`; set that as `site_url`.
3. No site yet? `@IMGAgent docs make` in an issue writes `docs/` and a complete `zensical.toml` as a pull request.
4. Zensical is pinned by the input `zensical-version` (default `0.1.1`). Raise the default here to update every
   repository at once after a test build; a repository can set its own. `deploy: false` only builds.

## Minimum levels per repository

Put a `[policy]` table into the repository's `SIGNOFF.toml`. You don't need any annotations for this, just `version = 4` and the policy. Both the PR check and `aisignoff lint` enforce it:

```toml
version = 4

[policy]
"*" = { human = "H1" }
G3  = { human = "H2", ai = "A1" }
```

The PR check reads the policy from the **base branch**, so a pull request can't lower the bar for itself.

## Updating from the trailer lines to the ticked declaration

The PR template no longer ends in trailer lines (`AI-Generation:`, `Reviewed-by:`, ...): the check derives everything from the ticked questions.

1. **Merge settings** of each repository: switch to merge commits with the message "Pull request title and description" (section 2 above). With squash merges and that message, the ticks still reach `main`, but the branch history doesn't.
2. **Repositories with their own PR template** (`.github/pull_request_template.md`): copy the new "AI declaration" section into it. The others use this repository's template.
3. **Open pull requests** fail the check until their description has the new "AI declaration" section and no trailer lines.
4. **History:** commits declared with trailers keep counting in the badges; nothing needs to be rewritten.

## Repositories on older formats

aisignoff reads `SIGNOFF.toml` with `version = 4`, and `version = 3` as the AI review scale before 1.0.0rc6, taken over to today's, until the repository is converted with [`convert-to-version-4.py`](https://github.com/industrial-mathematics-group/aisignoff/blob/main/examples/convert-to-version-4.py) ([Upgrading](https://industrial-mathematics-group.github.io/aisignoff/migrating/)). Until then, that repository's pull requests are read with this template on today's scale (from 1.0.0rc7 on; only a description with the boxes of the old template is read on the old scale), and from aisignoff 1.0.0rc8 on the checks for A2 and A3 (the line `AI-Review-Findings:` and the review report) apply to those boxes too. Convert each repository with a `SIGNOFF.toml` soon, moving inline tags into it first if it has any (`inline-tags-to-toml.py` before `convert-to-version-4.py`). The pull request that converts it changes `version`, which from 1.0.0rc7 on is protected like `[policy]`: commit the conversion to `main` directly, or let it through with `allow-policy-change`. A repository that still has `AIDECL.toml` (the name up to aidecl 0.3.0), version 1 (V0–V3) or version 2 (A-, A0–A3) fails *PR-signoff*, and *File-signoff* from aisignoff 1.0.0rc2 on, until it is converted, once, with the last release that does it:

```bash
uvx aisignoff==1.0.0rc1 migrate --dry-run
uvx aisignoff==1.0.0rc1 migrate
git add -A SIGNOFF.toml AIDECL.toml
```

Fill in the `TODO`s it leaves, run `aisignoff lint`, and switch README badges from `aidecl-*.json` to `aisignoff-*.json` (the workflow no longer writes the old names). Callers of the removed wrappers `ai-declaration.yml`, `aidecl.yml` and `ai-badge.yml` switch to **PR-signoff**, **File-signoff** and **Signoff-Badges** (required checks `pr-signoff / check` and `file-signoff / lint`); the inputs `aidecl-ref`, `aisignoff-ref` and `previous-ai-scale-until` are gone, use `aisignoff-version`. Commit trailers on `main` are never rewritten: the badges keep counting V0–V3 and `A-` trailers as before.

## How it fits together

```
feature branch ──(anything goes)──► PR into main
                                     │  template asks for the declaration
                                     │  "pr-signoff / check" validates it with aisignoff, sets ai:G* / human:H* / ai-review:A* labels
                                     ▼
                              merge commit ──► "title and description" on main, with the ticks
                                                 │
                                                 ▼
                              "Signoff-Badges" workflow ──► badges branch ──► README badges
```

- The declaration is only enforced for PRs into `main`. Commits on other branches are not checked. Draft PRs are checked too, so problems show up early, but only the state at merge counts.
- PRs opened by dependency update bots (Dependabot, and CompatHelper as `github-actions[bot]` from its branches `compathelper/*`) skip the check. Other bots, AI agents and other PRs of `github-actions[bot]` don't.
- Commits from before the policy have no declaration and are not counted in the badges. Commits declared with the trailer lines of earlier templates still count.

## Forgejo mirrors

The declarations are part of the merge commit messages, so they are mirrored as-is. The checks and badges run on GitHub only.
