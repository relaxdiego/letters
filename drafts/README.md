# Drafts

Unfinished thoughts wait here. Files in this folder are free-form — plain
text, no required format, usually a few bullets typed on a phone. Nothing in
this folder appears on the website or in the Wayback Machine.

A draft file is named `YYYY-MM-DD-HHMM-short-slug.md`: when it was written,
and a few words naming what it is about. The slug is only there so this folder
can be read at a glance. If one is clumsy, rename the file — nothing depends
on it.

One thing to know: this repository is public, so a draft here can be read by
anyone who finds the repository. A draft is unpublished, not private.

Another: drafts never live on `main`. They all live on one branch named
`drafts`, which is never merged (see `AGENTS.md`). So a draft is not in the
folder you get by default — on a computer, run `git switch drafts` to see
them, or pick `drafts` from the branch menu on the repository's GitHub page.

To publish a draft, turn it into a proper letter: on `main`, create a file in
`content/letters/` named `YYYY-MM-DD-short-slug.md`, with `title` and `date`
frontmatter (copy any finished letter to see the shape), dated the day you
publish it. Then delete the draft file from the `drafts` branch.
