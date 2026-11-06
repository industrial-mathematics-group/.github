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

A declaration reads like **G2 · H2 · A2**: AI-generated, reviewed line by line by a human, and reviewed by a new AI session.

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

| Level | Name | Meaning |
|---|---|---|
| **A0** | None | No AI reviewed the change. |
| **A1** | Self-check | The AI session that wrote the change ran the tests and checked its own output. |
| **A2** | New session | A **new** session, without the generating conversation, reviewed the final change and ran the tests. Any model counts, including the one that wrote the change. |
| **A3** | Different model, extensive | A **different model of similar or higher capability** reviewed the change extensively or benchmarked it: new tests, comparison with reference methods or solutions. It is the AI counterpart of H3. |

**AI-written content must at least be self-checked:** G1–G3 require at least A1.

**Human-written content (G0) has no self-check:** there is no generating session, so any AI review of it counts as A2 (or A3). G0 is never A1.

**Reviews must be committed:** When committing a change under A2-A3, the review results have to be added in a dedicated review folder as a markdown file. If not noted otherwise in the repository, it is `reviews` and the naming scheme is `commit#` or `pull#` with the respective commit or pull request number. If additional files like test codes need to be bundled, you can create a folder with the same name next to the review. The review folder should be excluded from the AI declaration.

**What counts as a review session (A2–A3):**
- It starts **without the generating conversation or its notes**. It sees only the issue, the diff and the repository.
- It **runs the test suite itself** and reviews the diff for correctness, edge cases and numerical soundness.
- Its findings are addressed before the PR is marked ready. Mention notable findings in the PR.
- If several models were involved, A3 needs a reviewing model that did not write any part of the change. If you're unsure whether a model counts as similarly capable, declare A2.

### Minimum levels per repository

A repository can require more through a `[policy]` table in its `SIGNOFF.toml`. The PR check and `aisignoff lint` both enforce it:

```toml
[policy]
"*" = { human = "H1" }               # default for every generation level
G3  = { human = "H2", ai = "A2" }    # stricter for autonomous agents
```

Without a policy, the minimum is H0 for everything, plus A1 for G1–G3. For G0, a minimum of A1 means A2.

### How to declare

The **AI declaration** section of the PR template asks a few questions. Tick what applies; the check derives the whole declaration from the ticks:

- **Generation:** tick exactly one of G0–G3.
- **AI models:** for G1–G3, tick the model families that wrote parts of the change (Claude Fable, Opus or Sonnet; GPT Astra, Sol or Terra), or tick **Other** and write the tool and model after it, e.g. `Other: Gemini 3 Pro (Google)`. For G0, leave them all unticked.
- **Human review:** tick every level you did. They build on each other, so H2 means H0, H1 and H2 are ticked.
- **AI review:** tick every check that happened; the highest one counts, so a review by a new session is A2 even without a self-check. Leave all unticked for A0. For A3, write the reviewing model after `Model:`. For A2, the reviewing model is taken to be the one that wrote the change, unless you name another after `Model, if not one of the above:`.
- **Responsible human:** the person who opens the PR, who also ticks *I take responsibility*. A PR opened by a bot or app account adds a line `Responsible person: @login` instead. Dependency update bots (Dependabot, CompatHelper) skip the check.

Keep the questions as they are in the template: the check needs all of them, reads only top-level boxes outside comments and code blocks, and fails if one is missing or appears twice. There are no trailer lines to fill in; the check fails on leftover `AI-Generation:`-style lines from the previous template.

The check runs on every PR into `main`, adds the labels `ai:G*`, `human:H*` and `ai-review:A*`, and shows the derived declaration in its job summary, in the same keys as `SIGNOFF.toml`. Our repositories merge PRs with a merge commit whose message is the **PR title and description**, so the ticked questions become part of the history on `main` and travel with the code into mirrors, forks and archived releases. Each repository can show badges summarising its declared history.

### Declaring files: SIGNOFF.toml

The PR declaration describes each *change*. Repositories that also contain results, data, notebooks or papers, or that are published in a single commit, declare the *files* instead. They use [aisignoff](https://github.com/industrial-mathematics-group/aisignoff) and a path-based `SIGNOFF.toml`, which works like `REUSE.toml`:

```bash
pipx install "git+https://github.com/industrial-mathematics-group/aisignoff"
aisignoff init -g G0 -H H2 -a A0 --reviewed-by "Jane Doe"            # default for everything
aisignoff annotate results/ -g G2 -H H1 -a A1 \
    --assisted-by "Claude Opus 5.5 (Anthropic)" \
    --ai-reviewed-by "Claude Opus 5.5 (Anthropic), same session" \
    --reviewed-by "Jane Doe"                                       # the exceptions
aisignoff lint                                                        # every file declared?
```

The same levels, rules and policy apply. Text files can carry the declaration in a header comment instead (`aisignoff annotate FILE --inline`).

Prefer not to use the command line? In repositories with the @claude assistant, the issue form **Claude: declare AI use for files** writes the declaration for you; see [Asking Claude on GitHub](../claude/README.md).

If a repository has a `SIGNOFF.toml`, **update it in the same PR** whenever your change alters how a file was created or reviewed. The *File-signoff* check fails if any file is left without a valid declaration.

**From earlier versions of this policy.** The previous version had the AI review levels A-, A0–A3; the scale moved up by one:

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

`aisignoff migrate` converts existing `SIGNOFF.toml` files and inline tags. Commits declared with the trailer lines of earlier templates stay as they are and are counted in the badges: V0–V3 trailers are converted, and `A-` counts as A0. For other trailers on the previous scale, the repository's badge workflow needs the last such commit (`previous-ai-scale-until`, see SETUP.md); otherwise they show one level too low.

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
