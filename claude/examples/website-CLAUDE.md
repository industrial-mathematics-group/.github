# Website: instructions for Claude

<!-- Starting point for the website repository: copy this file to CLAUDE.md in its root and keep it in step
     with src/content.config.ts. Field names here must match the collection schemas exactly. -->

This is the Industrial Mathematics Group website, built with [Astro](https://astro.build). People ask for new
entries through the "add something to the website" issue form. The org-wide rules (plain language, never invent
content, the PR declaration) apply; this file adds what is specific to the website.

## Where entries go

Every entry is one Markdown file in a content collection. The schemas are in `src/content.config.ts`; read it
before writing an entry, and follow it if it differs from the list below.

| Form choice | Folder | File name |
|---|---|---|
| Publication, Preprint, Thesis | `src/content/publications/` | `<year>-<first-author-surname>-<first-title-word>.md` |
| Talk or poster | `src/content/talks/` | `<yyyy-mm-dd>-<speaker-surname>.md` |
| Software | `src/content/software/` | `<repository-name>.md` |
| Person | `src/content/people/` | `<firstname>-<surname>.md` |
| News | `src/content/news/` | `<yyyy-mm-dd>-<short-slug>.md` |

Lower case, ASCII, words separated by `-` (e.g. `ü` → `ue`). Before you create a file, check that the entry doesn't
already exist (same DOI, title or person); if it does, update it instead and say so.

## Fields

- **publications:** `title`, `authors` (list, "Firstname Lastname", in the paper's order), `year`, `type`
  (`article` | `preprint` | `proceedings` | `thesis`), `venue`, `doi` (without `https://doi.org/`), `url`,
  `arxiv`, `repositories` (list of `owner/repo`). Body: the abstract as given, or empty.
- **talks:** `title`, `speaker`, `event`, `location`, `date` (YYYY-MM-DD), `kind` (`talk` | `poster` | `invited`),
  `slides` (URL). Body: optional abstract.
- **software:** `name`, `repository` (`owner/repo`), `description` (one sentence), `docs` (URL), `language`.
- **people:** `name`, `role` (`professor` | `postdoc` | `phd` | `student` | `alumnus`), `email`, `orcid`,
  `interests` (list). Body: a short bio, only from what the person provided.
- **news:** `title`, `date`. Body: the text as provided, lightly edited for spelling only.

Omit a field rather than inventing or guessing a value; ask in the issue if a required one is missing.

## Before you finish

1. `npm ci` (once), then `npm run build`. Fix errors in your own entry; if the build fails for other reasons, say so.
2. If `AIDECL.toml` exists, declare the new file as described in the org guide (G3, `-H TODO`).
3. In your reply, show the entry's fields as a short list so the person can check them without opening the file.
