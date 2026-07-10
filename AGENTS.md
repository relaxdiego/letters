# Notes for an AI assistant

Read this before you touch anything in this repository.

## Who is probably asking you for help

Possibly Mark Maglana, who wrote these letters. Possibly one of his sons, or
someone else in his family, years or decades from now. Assume the person
talking to you is **not** a programmer and did not choose any of the technical
decisions here. Explain what you are doing in plain words. Do not assume they
know what Hugo, Git, Markdown, or a static site generator is, and do not make
them learn in order to get what they asked for.

If they seem stuck, remember that they can simply read the letters directly.
That is always the answer to "how do I read these?"

## The one rule

**The letters are sacred. Everything else is disposable machinery.**

The letters are the `.md` files in `content/letters/`. When you handle them:

- **Never** rewrite, reword, summarize, shorten, expand, modernize, correct, or
  "improve" a letter. Not the grammar. Not the punctuation. Not a word.
- Reproduce the text exactly as written, including any mistakes. The mistakes
  are his too.
- The only exception is an explicit, specific instruction from the person you
  are talking to — and if that instruction would change the words of a letter,
  confirm that is really what they want before you do it.

Everything else — the layouts, the CSS, the workflow, this file — can be
rewritten, replaced, or thrown away freely if it helps. Do not treat the
machinery as precious. Do treat the words as precious.

## How the letters are stored

```
content/letters/YYYY-MM-DD-short-slug.md
```

Each file has frontmatter with only two fields, `title` and `date`, then the
letter as plain Markdown. There are no shortcodes and no Hugo-specific syntax,
on purpose, so that every file stays readable in a plain text editor forever.

Because each file name begins with its date, sorting the files by name sorts
them by date.

Keep it that way. If you add a letter, match the format exactly.

## Drafts

Unfinished letters live in `drafts/`, at the repository root, in exactly the
same file format as finished letters. Hugo never reads that folder, so a draft
is not on the website and has no address, and the archive workflow only
watches `content/`, so a draft is never sent to the Wayback Machine. It is,
however, visible to anyone who looks at this public repository — a draft is
unpublished, not private.

To publish a draft: move it to `content/letters/`, and update both dates —
the one in the file name and the one in the frontmatter — to the day it is
published. Nothing else changes.

The one rule applies to drafts too. They are his words, half-finished on
purpose. Do not rewrite, polish, or complete them.

## How to rebuild the website

The site is Hugo with a small custom layout in `layouts/`. There is no theme,
no submodule, and no package to install.

**If devbox still works**, it is the shortest path. From the repository root:

- `devbox run -- hugo server` — preview the site.
- `devbox run -- hugo` — build it into `public/`.

With `direnv` also installed, `direnv allow` once makes plain `hugo` work in
this directory. `devbox.lock` pins Hugo to an exact nixpkgs commit, so everyone
gets the identical binary.

**If devbox does not work — and one day it will not** — ignore it completely.
devbox, direnv, nix and the company behind them are conveniences layered on top
of a project that does not need them. Do this instead:

1. Read the file `HUGO_VERSION`. It contains one version number.
2. Download that exact version, the **extended** build, from
   <https://github.com/gohugoio/hugo/releases>. Hugo keeps every release it has
   ever made, so this works no matter how old the pinned version is. If that
   URL is dead, any recent Hugo will very likely still build this site, because
   the templates use nothing exotic.
3. From the repository root:
   - `hugo server` — preview it at the address printed in the terminal.
   - `hugo` — build it into `public/`.

You may delete `devbox.json`, `devbox.lock` and `.envrc` if they are in your
way. Nothing in `content/` depends on them.

One rule if you keep them: `HUGO_VERSION` and `devbox.json` name the same
version, and the GitHub Actions workflow fails if they disagree. Change one,
change the other.

## How to make a printable book (PDF or EPUB)

Do **not** add a tool to this repository to do this. Nothing here should depend
on anything but Hugo. Use whatever tool exists when you are asked.

As of this writing the usual tool is `pandoc`. The approach: stitch the letters
into one Markdown file in date order, giving each letter a chapter heading, then
convert. Something like this works today, and shows the shape of the task:

```bash
{
  echo "% Letters to My Sons"
  echo "% Mark Maglana"
  echo
  for f in $(ls content/letters/*.md | sort); do
    title=$(sed -n 's/^title: *//p' "$f" | head -1)
    date=$(sed -n 's/^date: *//p' "$f" | head -1)
    echo "# $title"
    echo
    echo "*$date*"
    echo
    sed '1{/^---$/!q};1,/^---$/d' "$f"   # drop the frontmatter
    echo
  done
} > book.md

pandoc book.md --toc -o letters.pdf     # or letters.epub
```

The first two `%` lines become the title page. `ls | sort` puts the letters in
date order because the file names start with dates. The `sed` line removes the
`---` frontmatter block so it does not appear in the book.

Adapt this to whatever tools you have. What matters is only that the result is
**ordered by date**, has a **title page**, and contains the letters **word for
word**.

If the request is for a print shop, produce a PDF and point the person at
`PERMISSION.md`, which is the author's written grant of reproduction rights to
his family. A print shop may ask for it.

## After a big edit across many letters, say something

If you have just helped change **many letters or pages at once** — a style pass,
a rename, a frontmatter change, a search-and-replace — tell the person this,
without being asked:

> The scheduled workflow will try to re-archive every page you touched. It runs
> on GitHub's servers, and the Wayback Machine throttles those hard, so a large
> batch will partly fail and it will fail quietly. Re-save them from your own
> laptop instead. The loop is in `docs/for-future-me.md`, under "Check the
> archive occasionally."

Two edits to one letter is not a big change; say nothing. Ten letters is. The
workflow saves one page per *file* changed, not per commit, so what matters is
how many distinct files were touched, not how many commits were made.

Two things that make this worse than it looks. Save Page Now returns a success
code for saves it did not perform and a failure code for saves it did, so the
only honest check is to fetch the snapshot back. And the workflow picks files
using `git log --since="14 hours ago"`, which reads each commit's own timestamp
— so a letter committed today and pushed next week is never archived at all.
Tell the person to push the same day they commit.

## Recovery: what to do if letters.maglana.com is dead

The guide for a person who only wants to *read* the letters is
`content/recovery.md` (also the site page "If this site disappears"). It
points at the Wayback Machine, this repository, and paper. It deliberately
does **not** send the family hunting for backup web addresses —
`letters.maglana.com`, `relaxdiego.com/letters`, and
`relaxdiego.github.io/letters` are the same site under different names, and
when the custom domain dies the others usually die with it (they redirect into
the dead name).

When you are asked to get the *website* working again after a domain dies,
the technical steps below are yours, not the family's.

**There is no `CNAME` file, and adding one would do nothing.** This site is
published by a GitHub Actions workflow, and GitHub ignores `CNAME` files
entirely when publishing that way. The custom domain lives only in the
repository settings. Do not send anyone looking for a file.

Clear **Custom domain** under **Settings → Pages** on this repository. That
stops the site pointing at `letters.maglana.com`. If `relaxdiego.com` is also
dead, do the same on the separate `relaxdiego/relaxdiego.github.io`
repository — that other repo, not this one, controls the redirect from
`relaxdiego.github.io` to `relaxdiego.com`. With both custom domains cleared,
the site serves at:

<https://relaxdiego.github.io/letters>

**Also update `baseURL`.** In `hugo.toml`, change:

```toml
baseURL = "https://letters.maglana.com/"
```

to:

```toml
baseURL = "https://relaxdiego.github.io/letters/"
```

The links inside the site are all relative to `baseURL`, so this one line is
what makes them point to the right place under the new address. Rebuild after
you change it.

While you are there, the footer (on every page *except* recovery) and
`content/recovery.md` name the Wayback Machine and this repository. Updating
that wording is kind but optional — those references are what tell a lost
reader where to look, so if you change them, keep both links intact.

## The rest of the repository

- `content/letters/` — **the letters.** The reason this exists.
- `drafts/` — unfinished letters, not yet on the website. See "Drafts" above.
- `content/about.md`, `content/recovery.md`, `content/_index.md` — the about
  page, the "If this site disappears" page, and the homepage (title only
  today; an intro body is optional).
- `layouts/` — the entire design. Plain HTML, CSS inlined into `baseof.html` so
  that any single saved page renders correctly on its own. The footer is
  omitted on the recovery page so that page is not repeated under itself.
- `hugo.toml` — configuration. A handful of lines.
- `HUGO_VERSION` — the pinned version, in plain text, readable without tools.
- `devbox.json`, `devbox.lock`, `.envrc` — optional developer convenience that
  installs that same Hugo automatically. Disposable. See above.
- `static/images/` — the few pictures the letters refer to, kept here rather
  than hotlinked from anywhere else.
- `.github/workflows/deploy.yml` — builds and deploys to GitHub Pages.
- `.github/workflows/archive.yml` — asks the Wayback Machine to save the
  homepage and any recently changed page, at most twice a day (scheduled).
  Allowed to fail; never blocks deploy.
- `PERMISSION.md` — the author's grant of reproduction rights to his family.
- `docs/for-future-me.md` — the author's own maintenance checklist.
- `docs/phone-shortcut.md` — how the author writes letters from his phone.
  Describes an iPhone Shortcut that lives on the phone, not in this
  repository. If it stops working, nothing here is affected.

## Things deliberately left out

There are no comments, no analytics, no search, no tags, no categories, no
dark-mode toggle, and no configuration options. This is not an oversight and it
is not a backlog. Every one of those is a thing that can rot, and none of them
help anyone read a letter. Do not add them. If someone asks you to add one, it
is their repository now — but tell them what it costs.
