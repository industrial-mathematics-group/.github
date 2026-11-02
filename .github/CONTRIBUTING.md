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
- PRs are merged with **squash merge**: the PR title and description become the commit message on `main`, including the AI declaration lines at the end.

## AI-assisted contributions

We **welcome the use of AI tools**: assistants, code generation and autonomous agents. The only requirement is that every contribution to `main` **declares honestly how it was created and how it was verified**.

The declaration is only required for pull requests into `main`. Work on your own branches as you like; the template and the automated check only apply when you open a PR.

### Generation levels: how was it written?

| Level | Name | Meaning |
|---|---|---|
| **G0** | Human | Written by a human. Spell-checking and single-line autocomplete don't count as AI use. |
| **G1** | AI-assisted | A human wrote the change; AI suggested or edited parts of it. |
| **G2** | AI-generated | AI wrote substantial parts; a human directed the work and edited the result. |
| **G3** | AI-autonomous | An agent produced the change largely end to end, with little human editing. |

Choose the **highest** level that applies to any substantial part of the PR.

### Verification levels: how was it checked?

| Level | Name | Meaning |
|---|---|---|
| **V0** | Human | A human ran the tests and reviewed the change line by line. |
| **V1** | Independent agent, same model | A **fresh** agent session of the same model verified the change (see below). |
| **V2** | Independent agent, different model | An agent based on a **different, comparably capable** model verified the change. |
| **V3** | Human + independent agent | Both V0 and at least one of V1 or V2. |

The levels describe *who* verified the change, not a ranking; V3 is the most thorough. Unverified changes are not merged.

**What counts as an independent agent (V1, V2):**
- It starts in a **new session without the generating conversation or its notes**. It sees only the issue, the diff and the repository.
- It **runs the test suite itself** and reviews the diff for correctness, edge cases and numerical soundness.
- Its findings are addressed before the PR is marked ready. Mention notable findings in the PR.

Maintainer review is separate from this declaration: every PR is still reviewed and merged by a maintainer.

### How to declare

The PR template ends with a block of trailer lines. Fill them in and **delete lines that don't apply**:

```
AI-Generation: G2
AI-Verification: V3
Assisted-by: Claude Opus 5.5 (Anthropic)
Verified-by: GPT-5 (OpenAI), independent session
Tested-by: Jane Doe <jane@example.org>
```

| Line | Required for |
|---|---|
| `AI-Generation: G0`–`G3` | every PR |
| `AI-Verification: V0`–`V3` | every PR |
| `Assisted-by:` tool and model | G1–G3 (one line per tool) |
| `Verified-by:` verifying model and session | V1–V3 |
| `Tested-by:` the human who tested | V0 and V3 |

These lines must stay at the **very end** of the PR description. On squash merge they become [git trailers](https://git-scm.com/docs/git-interpret-trailers) in the commit on `main`, so the declaration travels with the code into mirrors, forks and archived releases.

An automated check validates the block on every PR into `main` and adds the labels `ai:G*` and `verified:V*`. Each repository can show a badge summarising its declared history.

### Responsibility

- **You are responsible for what you submit**, whoever or whatever wrote it. AI cannot take responsibility for a contribution; the human who opens the PR does.
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
