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
| `.github/workflows/signoff-request.yml`, `.github/ISSUE_TEMPLATE/signoff-request.yml` | Reusable workflow *Signoff-Request* and its issue form: a `SIGNOFF.toml` change from an issue, without AI |
| `.github/workflows/signoff-assistant.yml` | Reusable workflow *Signoff-Assistant*: the `@signoff` assistant, which turns a request in plain words into the form's answers with the Claude API and hands them to Signoff-Request |
| `.github/workflows/claude.yml`, `claude/` | The @claude assistant (see [below](#the-claude-assistant)) |
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

1. **Once for the organisation:** the workflow installs aisignoff from PyPI, pinned by the input `aisignoff-version` of `.github/workflows/file-signoff.yml` (and of `claude.yml`, keep both at the same version). Raise the default there to update every repository at once, after checking the aisignoff changelog for **Breaking** changes; a repository can set `aisignoff-version` itself to differ.
2. **Per repository:** add `SIGNOFF.toml` (`aisignoff init …`, `aisignoff annotate …`) and the **File-signoff** workflow (*Actions → New workflow*). It runs `aisignoff lint` on every push, PR and merge queue run, and on pushes to `main` it publishes the badges:

```markdown
[![AI generation (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-generation.json)](https://github.com/industrial-mathematics-group/aisignoff)
[![Human review (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-human-review.json)](https://github.com/industrial-mathematics-group/aisignoff)
[![AI review (files)](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/industrial-mathematics-group/<repo>/badges/aisignoff-ai-review.json)](https://github.com/industrial-mathematics-group/aisignoff)
```

3. Optionally require the status check **`file-signoff / lint`** on `main`.
4. Optionally add **Signoff-Request** (*Actions → New workflow*), see [below](#signoff-requests-from-issues).
5. Optionally add **Signoff-Assistant (@signoff)** next to it, see [below](#the-signoff-assistant).

Both systems can run side by side: the PR check and its badges describe changes, aisignoff describes files. They use different badge files on the same `badges` branch.

### Signoff requests from issues

Lets group members declare or attest files without TOML, git or AI. They fill in the issue form
**Signoff: declare or attest files** (an organisation default, like the PR template); the *Signoff-Request*
workflow writes the answers with `aisignoff annotate` or `aisignoff attest` (or a `[[commits]]` correction),
checks the result with `aisignoff lint`, pushes the branch `signoff-request/issue-<n>` and opens a pull request
from the PR template. Nothing is merged without a human:

```
issue form ──► Signoff-Request: aisignoff annotate / attest, lint ──► branch + PR (G0 ticked, "Responsible person: @requester")
   ▲  comment: what to fix                                                   │
   └──────── edit the issue: runs again, updates the PR                       ▼
                       requester reviews Files changed, ticks H0–H3 ──► pr-signoff / check ──► merge
```

- **Per repository:** *Actions → New workflow* → **Signoff-Request**. It needs *File-signoff* and a `SIGNOFF.toml`.
  If your File-signoff workflow file isn't called `file-signoff.yml`, set `dispatch-workflows` in the caller.
- **Bot account (optional, recommended).** By default the bot is `github-actions[bot]`. To give it its own name,
  create a GitHub App once for the organisation (*Organisation settings → Developer settings → GitHub Apps → New*):
  no webhook, repository permissions **Contents**, **Pull requests** and **Issues** read and write; install it on
  the repositories with Signoff-Request. Then add the organisation variable `SIGNOFF_APP_ID` (its App ID) and the
  organisation secret `SIGNOFF_APP_PRIVATE_KEY` (a private key generated on the app's page), both for the same
  repositories. The caller passes them on; without them the workflow falls back to `github-actions[bot]`.
- **With the app,** its pull requests start File-signoff and PR-signoff like anyone's; PR-signoff fails until the
  requester ticks, and reads "Responsible person" because the author is a bot.
- **Without the app,** the workflow needs "Allow GitHub Actions to create and approve pull requests" (section 1; a
  repository can switch it off under its own *Settings → Actions → General*). GitHub starts no workflows for pull
  requests opened with `GITHUB_TOKEN`, so the workflow starts File-signoff on the branch itself
  (`workflow_dispatch`), and *PR-signoff* runs once the requester edits the description to tick their review;
  until then a required `pr-signoff / check` stays pending, which is the intended gate. These pull requests are
  opened by `github-actions[bot]`, but unlike CompatHelper's (branches `compathelper/*`) they are not exempt from PR-signoff.
- **A pull request that changes only `SIGNOFF.toml`** is checked like any other: *PR-signoff* requires a complete
  description for every pull request (G0 for the bot's change, the requester's H level, and the `[policy]` of
  `main`), whatever it changes. Nothing to configure.
- Only people with write access get a branch; others get a comment. The form's text is passed to aisignoff as
  arguments, never to a shell, and no code from the repository runs.
- Repositories with their own `.github/ISSUE_TEMPLATE/` folder don't see the organisation's forms: copy
  `signoff-request.yml` into it. Optionally create a label `signoff-request`, which the form applies if it exists.

### The @signoff assistant

Lets group members ask for the same pull request in their own words instead of filling in the form. They write
`@signoff` in a new issue or a comment, e.g. "@signoff I wrote results/convergence/ with Claude Opus 5.5, which
also checked its work; I reran the study and read the important parts." *Signoff-Assistant* sends the
conversation, the repository's file list, `SIGNOFF.toml` and the recent commits to the Claude API, which fills in
the form's answers. They are checked like the form's, and with `aisignoff annotate` / `attest` and `lint` on a
scratch copy of `main`; what fails goes back to Claude once, and what is still missing or ambiguous becomes
questions in a comment, answered with another `@signoff` comment. Complete answers go to *Signoff-Request*, which
checks them again and opens the pull request. Claude only suggests; the requester reviews, ticks and merges:

```
@signoff + plain words ──► Claude: the form's answers ──► form checks + aisignoff (scratch copy of main)
   ▲   comment: questions, or what fails      │ missing, ambiguous or refused     │ complete
   └──────────────────────────────────────────┘                                   ▼
          Signoff-Request: aisignoff, lint ──► branch + PR (G1, model and A0 ticked) ──► requester ticks H0–H3 ──► merge
```

- **Once for the organisation:** the organisation secret `ANTHROPIC_API_KEY` from
  [the @claude assistant](#once-for-the-organisation) (step 2 and 3; an API key in a Console workspace with a
  spend limit). Give the repositories with Signoff-Assistant access to it. The assistant reads no other secret
  but the bot app's, and the key is only in the step that calls the model.
- **Per repository:** *Signoff-Request* first, then *Actions → New workflow* → **Signoff-Assistant (@signoff)**.
  Without access to the secret, `@signoff` gets a comment that points to the form, and nothing else happens.
  If your File-signoff workflow file isn't called `file-signoff.yml`, set `dispatch-workflows` as in the
  Signoff-Request caller.
- **Model:** `claude-sonnet-5-5` by default (inputs `model` and `model-name`, change both together; `effort`).
  A request costs a few cents, mostly for the file list.
- **The pull request** ticks **G1**, the model and **A0** instead of G0, since an AI turned the requester's words
  into the answers and no other AI session reviewed them, and says so above the diff. Requesters can correct the ticks.
- **Security:** only people with write access are answered, and only their text, and the bots' own comments,
  reach the model; issue text by anyone else is left out. The model has no tools: it fills in a fixed JSON
  schema whose values are checked and passed to aisignoff as separate arguments, never to a shell. Its comments
  can't mention other people or carry the workflows' hidden markers. No code of the repository runs.
- Issues made with the form and pull requests are left alone; edited comments don't start it again (write a new one).

## The @claude assistant

Lets group members ask Claude in issues and pull requests, mainly to write `SIGNOFF.toml` declarations and
website entries without touching TOML or git. Claude runs in GitHub Actions through the official
[Claude GitHub App](https://github.com/apps/claude) and [claude-code-action](https://github.com/anthropics/claude-code-action).
It pushes a branch and posts a "Create PR" link; the person who asked opens the PR, ticks their review level and merges.
For declaring files, the [Signoff-Request](#signoff-requests-from-issues) form does the same without AI.

| Path | Purpose |
|---|---|
| `.github/workflows/claude.yml` | Reusable workflow: installs aisignoff, loads the org guide, runs Claude |
| `workflow-templates/claude.yml` | One-click caller workflow for each repository |
| `claude/ORG-GUIDE.md` | Claude's instructions for every repository: AI levels, aisignoff, the PR declaration |
| `claude/README.md` | Guide for group members: how to ask Claude |
| `claude/examples/website-CLAUDE.md` | Starting point for the website repository's `CLAUDE.md` |

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
3. Test it: open an issue and write "@claude summarise what this repository contains". A progress comment
   should appear within a minute or two.

### Settings you may want to change

In the caller workflow (`with:`):

| Input | Default | Use |
|---|---|---|
| `model` / `model-name` | `claude-sonnet-5-5` / `Claude Sonnet 5.5 (Anthropic)` | A stronger model, e.g. `claude-opus-5-5` / `Claude Opus 5.5 (Anthropic)`. Change both together: `model-name` is what Claude writes into declarations. |
| `node-version` | none | `"22"` for repositories that build with Node (the website) |
| `max-turns` | `40` | Cost guard per run |
| `aisignoff-version` | `1.0.0rc8` | The aisignoff release Claude uses; keep it at File-signoff's |
| `guide-ref` | `main` | Pin the guide to a tag |

Organisation-wide behaviour (levels, wording, PR declaration) is changed in `claude/ORG-GUIDE.md`; every
repository picks it up on its next run.

### How a request runs

```
issue or comment with "@claude"
   │  caller workflow in the repository (skipped unless "@claude" is in the text)
   ▼
reusable workflow: checkout ─► pip install aisignoff ─► ORG-GUIDE.md as ~/.claude/CLAUDE.md
   ▼
Claude (as the Claude app): reads issue + repo CLAUDE.md, runs aisignoff annotate / lint or edits files,
pushes branch claude/issue-<n>-…, posts "Create PR" link with the PR template pre-ticked (G3, model, A0)
   ▼
person opens PR ─► ticks H0–H3 ─► pr-signoff / check green ─► merge
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
