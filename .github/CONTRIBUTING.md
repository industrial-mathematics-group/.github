# Contributing

Thank you for contributing to a project of the **Industrial Mathematics Group**! These guidelines apply to every repository in the organisation unless a repository has its own `CONTRIBUTING.md`.

## Quick start

1. **Open an issue first** for anything larger than a small fix, so we can agree on the approach before you invest time.
2. **Fork and branch.** Work on a feature branch in your fork; never push to `main` directly.
3. **Make your change** with tests and, where it applies, documentation.
4. **Run the checks locally** (see below).
5. **Open a pull request into `main`** and fill in the template, including the [AI declaration](#ai-assisted-contributions).

## Development workflow (Julia packages)

```bash
git clone https://github.com/<your-user>/<Package>.jl.git
cd <Package>.jl
julia --project -e 'using Pkg; Pkg.instantiate()'
julia --project -e 'using Pkg; Pkg.test()'
```

- **Tests:** new functionality needs tests; bug fixes need a test that fails without the fix. For numerical methods, prefer convergence or exact-solution tests over golden numbers.
- **Documentation:** public functions need docstrings. If you change behaviour, update the docs in `docs/src`. Build them with
  `julia --project=docs -e 'using Pkg; Pkg.develop(path="."); Pkg.instantiate(); include("docs/make.jl")'`.
- **Formatting:** if the repository contains a `.JuliaFormatter.toml`, run JuliaFormatter before committing.
- **Compatibility:** don't raise minimum Julia or dependency versions without saying so in the PR.

## Pull requests

- Keep each pull request focused on one change. Large or unfocused PRs may be asked to be split.
- Describe *what* changes and *why*, and link the related issue.
- You must be able to explain your change and answer review questions in your own words.
- PRs are merged with a **merge commit**, so the full history of the branch is kept. The PR title and description become the merge commit's message on `main`, including the ticked AI declaration.

## AI-assisted contributions

We **welcome the use of AI tools**: assistants, code generation and autonomous agents. The requirement is that every contribution to `main` **declares honestly how it was created and how it was reviewed**, on three axes:

- **G**: how much of it an AI generated;
- **H**: how thoroughly a human reviewed it (always required: a human is responsible for every contribution);
- **A**: how thoroughly an AI reviewed it.

A declaration reads like **G2 · H2 · A1**: AI-generated, reviewed line by line by a human, and checked by a new AI session.

The declaration is only required for pull requests into `main`. Work on your own branches as you like; the template and the automated check only apply when you open a PR.

### G: generation

| Level | Name | Meaning |
|---|---|---|
| **G0** | Human | Written by a human. Spell-checking and single-line autocomplete don't count as AI use. |
| **G1** | AI-assisted | A human wrote the change; AI suggested or edited parts of it. |
| **G2** | AI-generated | AI wrote substantial parts; a human directed the work and edited the result. |
| **G3** | AI-autonomous | An agent produced the change largely end to end, with little human editing. |

Choose the **highest** level that applies to any substantial part of the PR.

### H: human review

The levels describe what the human **did**, and each includes the ones below it.

| Level | Name | What the human did |
|---|---|---|
| **H0** | Plausibility check | Looked at the result as a whole: it does what was asked, runs, and the output is plausible. |
| **H1** | Tested | Ran the tests or reproduced the key results themselves, and read the important parts (interfaces, core numerics). |
| **H2** | Reviewed | Read **every changed line**, understands it, and checked edge cases. |
| **H3** | Validated | H2, plus an **independent check**: wrote new tests, compared against a reference solution or method (analytical solution, convergence order, literature), or a second person reviewed it. |

For results and data:
- **H1:** the results were regenerated or reproduced.
- **H2:** every figure and number was checked against its source.
- **H3:** the results were reproduced independently, by another person or with another method.

### A: AI review

The A axis counts only review that is **independent of the session that wrote the change**. What the generating session checks itself (running the tests, rereading its result) is part of generating, expected from G2 on, and no AI review. Each level includes the ones below it.

| Level | Name | Meaning |
|---|---|---|
| **A0** | No independent AI review | No AI reviewed the change, or only the session that wrote it checked its own work. |
| **A1** | Context-free check | A **new** session, without the generating conversation, reviewed the whole change against the task and ran the tests. Any model counts, including the one that wrote the change. |
| **A2** | Check by a different model | A1, done by a **different, capable model**, which commits **one review report**. |
| **A3** | Validated by a different model | A2, and the reviewer produced **new evidence**: new tests, edge cases or benchmarks, a comparison with a reference method or solution, a result derived another way, counterexamples. It commits the evidence with the report. It is the AI counterpart of H3. |

**Context-free:** the reviewer gets the content and the task (the issue, the diff, the repository), not the generating conversation, its notes or the author's conclusions. A subagent with a fresh context counts. A second model that reviews inside the generating conversation doesn't: that is A0.

**Different model:** a model that did not write any part of the change. Another model of the same vendor counts (Fable reviewing Opus); another version of the same model doesn't. For human-written changes (G0), every model is a different one.

**Capable:** in the same tier as the highest model that wrote the change, or above; for G0, tier 1 or 2. A check by a weaker model counts as A1. The table is a guideline as of November 2026, by name only: a newer version of a name stays in its tier until the table changes. Models that aren't listed are placed by the repository's maintainers.

| Tier | Claude (Anthropic) | ChatGPT (OpenAI) | Capable reviewer for |
|---|---|---|---|
| 1 | Fable, Mythos | Astra | everything |
| 2 | Opus | Sol | changes written by tier 2 to 4, and G0 |
| 3 | Sonnet | Terra | changes written by tier 3 or 4 |
| 4 | Haiku | Luna | changes written by tier 4 |

**Reviews are committed (A2–A3).** The review goes into a dedicated review folder as a Markdown file. If not noted otherwise in the repository, the folder is `reviews` and the file is named `commit#.md` or `pull#.md`, with the respective commit or pull request number. The review folder is excluded from the AI declaration.

- **A2: exactly one Markdown file,** with only the results. In this organisation it follows the [report template](https://github.com/industrial-mathematics-group/aisignoff/blob/main/examples/review-report.md).
- **A3: the same report, plus the evidence:** new tests go into the test suite; other files (scripts, derivations, benchmark results) go into a folder with the report's name next to it.
- **A1** needs no report.

The template keeps two lists apart:

- **Findings in this change:** what the change itself introduces or makes worse, each with its severity and whether it is fixed or open. The line `Highest finding in this change:` at the top of the report gives the highest severity among them, or `none`.
- **Outside this change:** problems the reviewer met that were there before the change, of any severity, each with the reason why the change isn't its cause. They don't count towards the highest finding, so a review can report a major problem elsewhere in the code without holding up the change. Maintainers read this section to see that it really is separate from the change.

**Findings and fixes.** A review counts once each of its findings is fixed or listed as open in the report. Findings are rated on four levels, after IEEE Std 1044-2009:

| Severity | Meaning | Code | Research and writing |
|---|---|---|---|
| **critical** | The work fails its purpose, or does harm | Data loss or corruption, a security hole, a crash or wrong results in the main use case | A central result or conclusion is wrong; a proof fails; data are misused |
| **major** | A wrong result or behaviour in a case that matters, with no easy way around it | A wrong result for a supported input; a broken feature; a missing check that lets invalid input through | A wrong number, formula or figure that a reader would rely on; a gap in an argument; a reference that doesn't say what is claimed |
| **minor** | A limited effect, or an easy way around it | A wrong result in a rare edge case; a misleading error message; a missing test | An imprecise statement; an inconsistency between text and table that doesn't change a result |
| **trivial** | No effect on results or behaviour | Naming, style, comments, typos | Wording, layout, typos |

A fix for a minor or trivial finding needs no review of its own. A **major or critical finding in the change ends the review**: fix it, have the change reviewed again in a new session, and commit that review's report in place of the old one. A repository can move the threshold with `recheck` in the `[review]` table of its `SIGNOFF.toml`.

The declaration repeats the report's highest finding in a line `AI-Review-Findings:`, in the PR template below the AI review boxes and in a commit message next to `AI-Review:`. The check (aisignoff 1.0.0rc7 or later) needs that line for A2 and A3, and the report named after `Model and report:` (a file of the PR, or a link). It fails when the line says a severity from the threshold on. It never reads the report: it is a guard against mistakes, and maintainers decide whether a report is in order.

### Minimum levels per repository

A repository can require more through a `[policy]` table in its `SIGNOFF.toml`. The PR check and `aisignoff lint` both enforce it:

```toml
[policy]
"*" = { human = "H1" }               # default for every generation level
G3  = { human = "H2", ai = "A1" }    # stricter for autonomous agents
```

Without a policy, the minimum is H0 for everything, and no AI review is required. A minimum means the same for every generation level.

### How to declare

The **AI declaration** section of the PR template asks a few questions. Tick what applies; the check derives the whole declaration from the ticks:

- **Generation:** tick exactly one of G0–G3.
- **AI models:** for G1–G3, tick the model families that wrote parts of the change (Claude Fable, Opus or Sonnet; GPT Astra, Sol or Terra), or tick **Other** and write the tool and model after it, e.g. `Other: Gemini 3 Pro (Google)`. For G0, leave them all unticked.
- **Human review:** tick the highest level you did. The levels build on each other, so H2 says H0 and H1 too; ticking those as well changes nothing.
- **AI review:** tick the highest level that was done, or A0; the highest ticked one counts, and none ticked means A0. For A2 and A3, write the reviewing model and the report after `Model and report:` (e.g. `GPT Sol (OpenAI), reviews/pull12.md`); it must be a model that didn't write the change. Write the report's highest finding in this change after `AI-Review-Findings:`. For A1, the reviewing model is taken to be the one that wrote the change, in a new session, unless you name another after `Model, if not one of the above:`.
- **Responsible human:** the person who opens the PR; by ticking a human review level they take responsibility for the change, however it was created. A PR opened by a bot or app account adds a line `Responsible person: @login` naming a human instead. Dependency update bots (Dependabot, CompatHelper) skip the check.

Keep the questions as they are in the template: the check reads only boxes outside comments and code blocks, and fails if a level is missing. There are no trailer lines to fill in; leftover `AI-Generation:`-style lines from the previous template next to ticked boxes are an error.

The check is [aisignoff](https://github.com/industrial-mathematics-group/aisignoff) (`aisignoff lint --declare-from`), which reads the description exactly as the merge commit will carry it. In repositories with a `SIGNOFF.toml` it also checks every file (and with `[history]` every line) of the merged result, applies the repository's `[policy]` from `main`, and doesn't let a PR change `[policy]`, `exclude` or `[history]`: maintainers commit such changes to `main` directly.

The check runs on every PR into `main`, adds the labels `ai:G*`, `human:H*` and `ai-review:A*`, and shows the derived declaration in its job summary, in the same keys as `SIGNOFF.toml`. Our repositories merge PRs with a merge commit whose message is the **PR title and description**, so the ticked questions become part of the history on `main` and travel with the code into mirrors, forks and archived releases. Each repository can show badges summarising its declared history.

### Declaring files: SIGNOFF.toml

The PR declaration describes each *change*. Repositories that also contain results, data, notebooks or papers, or that are published in a single commit, declare the *files* instead. They use [aisignoff](https://github.com/industrial-mathematics-group/aisignoff) and a path-based `SIGNOFF.toml`, which works like `REUSE.toml`:

```bash
pipx install "git+https://github.com/industrial-mathematics-group/aisignoff"
aisignoff init -g G0 -H H2 -a A0 --reviewed-by "Jane Doe"            # default for everything
aisignoff annotate results/ -g G2 -H H1 -a A1 \
    --assisted-by "Claude Opus 5.5 (Anthropic)" \
    --ai-reviewed-by "Claude Opus 5.5 (Anthropic), new session without the generating conversation" \
    --reviewed-by "Jane Doe"                                       # the exceptions
aisignoff lint                                                        # every file declared?
```

The same levels, rules and policy apply. aisignoff reads declarations only from `SIGNOFF.toml` and from git, never from the content of a file: a declaration in a header comment (the inline tags up to 1.0.0rc6) no longer counts, see [Upgrading](https://industrial-mathematics-group.github.io/aisignoff/migrating/).

Prefer not to use the command line? In repositories with the *Signoff-Request* workflow, the issue form **Signoff: declare or attest files** opens the pull request for you, without AI; you review and merge it. In repositories with the @claude assistant you can also ask Claude; see [Asking Claude on GitHub](../claude/README.md).

If a repository has a `SIGNOFF.toml`, **update it in the same PR** whenever your change alters how a file was created or reviewed. The *File-signoff* check fails if any file is left without a valid declaration.

**From earlier versions of this policy.** Until November 2026 (aisignoff before 1.0.0rc6, `SIGNOFF.toml` version 3), the AI review scale counted the self-check of the generating session:

| Previous | Now |
|---|---|
| A0 (none) | A0 |
| A1 (self-check) | A0 |
| A2 (new session) | A1, or A2 if the reviewer was a different, capable model and its report is committed |
| A3 (different model, extensive) | A3 if it produced new evidence, else A2 |

aisignoff keeps reading a `SIGNOFF.toml` of version 3, and the pull requests of its repository, on that scale and shows today's levels, until the repository is converted with [`convert-to-version-4.py`](https://github.com/industrial-mathematics-group/aisignoff/blob/main/examples/convert-to-version-4.py) ([Upgrading](https://industrial-mathematics-group.github.io/aisignoff/migrating/)).

The version before that had the AI review levels A-, A0–A3; the scale moved up by one (to the previous scale above):

| Previous | Now |
|---|---|
| A- (none) | A0 |
| A0 (self-check) | A1 (for G0: fill it in, since human-written content has no self-check) |
| A1 (independent session) | A2 |
| A2 (different model) | A2, or A3 if the review was extensive or benchmarked |
| A3 (different model with benchmark) | A3 |

The first version had one verification axis, V0–V3:

| Old | Now |
|---|---|
| V0 | H2, and A0 for G0 (for G1–G3 the AI review is unknown: fill it in) |
| V1, V2 | A2 |
| V3 | H2 + A2 |

Convert these older `SIGNOFF.toml` files (or `AIDECL.toml`) and inline tags once with `uvx aisignoff==1.0.0rc1 migrate`, the last release that does. Commits declared with the trailer lines of earlier templates stay as they are and are counted in the badges: V0–V3 trailers are converted, and `A-` counts as A0; other trailers on the previous scale are read on the current one.

### Responsibility

- **You are responsible for what you submit**, whoever or whatever wrote it. AI cannot take responsibility for a contribution; the human who opens the PR (or is named as the responsible person) does.
- Make sure AI output doesn't reproduce code under an incompatible licence. Enable licence or duplication filters where your tool offers them.
- Answer review questions yourself; don't paste AI-generated replies without reading them.
- Never put secrets, unpublished data or personal data into third-party AI tools.

## Reporting bugs and security issues

- **Bugs and feature requests:** open an issue using the templates.
- **Security issues:** please don't open a public issue; see [SECURITY.md](SECURITY.md).

## Code of conduct

Everyone taking part in our projects is expected to follow our [Code of Conduct](CODE_OF_CONDUCT.md).

## Licence

By contributing, you agree that your contribution is licensed under the licence of the repository you contribute to (see its `LICENSE` file).
