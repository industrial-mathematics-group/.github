# Asking Claude on GitHub

In our repositories you can ask Claude, an AI assistant, to do the fiddly parts for you: write the AI
declaration (`SIGNOFF.toml`) for your files, add a publication to the website, or make a small change.
You describe what you want in an issue; Claude prepares it on a branch; **you check it and open the pull request.**
You never have to write TOML or use git.

You need to be a member of the organisation with write access to the repository. Claude ignores requests from
anyone else.

## The three ways to ask

| You want to … | Do this |
|---|---|
| Declare how files were made (AI levels) | *Issues → New issue →* **Claude: declare AI use for files** |
| Add something to the website | In the `website` repository: *Issues → New issue →* **Claude: add something to the website** |
| Anything else | *Issues → New issue →* **Claude: ask for something else**, or write `@claude` followed by your request in any issue or pull request comment |

Writing `@claude` anywhere in a comment starts Claude, so only use it when you mean it.

## What happens next

1. Claude answers in the issue within a minute or two, with a comment that shows its progress.
2. If something is unclear, it asks you a question. Reply in the issue, starting with `@claude`.
3. When it's done, its comment says what it changed and has a **Create PR** link.
4. Click the link and then **Create pull request**. The description is already filled in.
5. In the pull request, look at *Files changed*. Then tick in the description:
   - your **human review** level (H0–H3) for Claude's change, i.e. how carefully you checked what Claude did, and
   - **I take responsibility for this change**.

   The *PR-signoff* check turns green once these are ticked.
6. Merge it (*Merge pull request*). That's it.

Want something changed? Comment `@claude ...` in the pull request, e.g. `@claude also include figures/fig4.pdf`.
Claude adds a commit to the same pull request.

## Declaring files you upload

If the files aren't in the repository yet:

1. *Add file → Upload files*, drag your files in, choose **Create a new branch** and **Propose changes**.
2. Open the pull request as usual.
3. In the pull request, write a comment such as:

   > @claude please declare these files: G2, written with Claude Opus 5.5, I reproduced the key results (H1),
   > the same session checked its work (A1). Responsible: Jane Doe.

Claude adds the declaration to your pull request.

## Two declarations, not one

- **The form's answers** describe *your files*: how much AI helped make them and how you checked them.
  Claude only writes down what you said.
- **The ticks in the pull request** describe *Claude's change* to the repository (for example the edit to
  `SIGNOFF.toml`). Claude ticks its own part (G3, its model, A1); you tick how you checked it (H).

If you're unsure which level fits, see the [AI policy](https://github.com/industrial-mathematics-group/.github/blob/main/.github/CONTRIBUTING.md#ai-assisted-contributions),
or ask Claude in the issue.

## Good to know

- **Issues in public repositories are public.** Don't put unpublished results, reviews or personal data into an
  issue. Use a private repository for those.
- Claude **can't** merge, approve or delete anything, and never pushes to `main`. Nothing changes until you merge.
- Claude can be wrong. You are responsible for what you merge, so look at *Files changed* before you do.
- Each request costs the group a little money (cents to a few euros). Prefer one clear request over many small ones.
- Something went wrong? The progress comment links to the run log. Ask Pascal if you're stuck.
