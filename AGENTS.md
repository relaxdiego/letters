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

## Recovery: what to do if letters.maglana.com is dead

The site is published at the custom domain `letters.maglana.com`. Domains must
be paid for and eventually one will not be.

Two custom domains sit in front of this site, in two *different* repositories,
and they chain together:

    relaxdiego.github.io/letters  →  relaxdiego.com/letters  →  letters.maglana.com

**There is no `CNAME` file, and adding one would do nothing.** This site is
published by a GitHub Actions workflow, and GitHub ignores `CNAME` files
entirely when publishing that way. The custom domain lives only in the
repository settings. Do not send anyone looking for a file.

**Clear the custom domain in the settings, not in the repository:**

1. On `relaxdiego/letters` (this repository), go to **Settings → Pages** and
   clear the **Custom domain** box. This removes the hop to
   `letters.maglana.com`.
2. If `relaxdiego.com` has also expired, do the same on
   `relaxdiego/relaxdiego.github.io`, which is a **separate repository**. Its
   custom domain is what redirects every project page under this account to
   `relaxdiego.com`. Clearing it here has no effect on that.

With both cleared, the site serves directly at:

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
what makes them point to the right place under the new address. Nothing else
needs to change.

While you are there, the footer on every page and the `content/recovery.md`
page both name the old domain. Updating that wording is kind but optional —
those references are what tell a lost reader where to look, so if you change
them, keep the Wayback Machine and GitHub links intact.

## The rest of the repository

- `content/letters/` — **the letters.** The reason this exists.
- `content/about.md`, `content/recovery.md`, `content/_index.md` — the about
  page, the "If this site disappears" page, and the homepage introduction.
- `layouts/` — the entire design. Plain HTML, CSS inlined into `baseof.html` so
  that any single saved page renders correctly on its own.
- `hugo.toml` — configuration. A handful of lines.
- `HUGO_VERSION` — the pinned version, in plain text, readable without tools.
- `devbox.json`, `devbox.lock`, `.envrc` — optional developer convenience that
  installs that same Hugo automatically. Disposable. See above.
- `static/images/` — the few pictures the letters refer to, kept here rather
  than hotlinked from anywhere else.
- `.github/workflows/deploy.yml` — builds, deploys, then asks the Wayback
  Machine to save the changed pages. The archive step is allowed to fail.
- `PERMISSION.md` — the author's grant of reproduction rights to his family.
- `docs/for-future-me.md` — the author's own maintenance checklist.

## Things deliberately left out

There are no comments, no analytics, no search, no tags, no categories, no
dark-mode toggle, and no configuration options. This is not an oversight and it
is not a backlog. Every one of those is a thing that can rot, and none of them
help anyone read a letter. Do not add them. If someone asks you to add one, it
is their repository now — but tell them what it costs.
