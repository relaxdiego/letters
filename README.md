# Letters

This repository holds letters from your father, Mark Maglana, written to his
sons.

## The letters are right here, and you do not need any software to read them

Open the folder `content/letters/`. Every file in it is one letter. Each file
name starts with the date it was written, so they sort themselves in order.

These files end in `.md`, but do not let that worry you. They are plain text.
Click any one of them on GitHub, or open it in any text editor on any computer,
and you will simply see the letter — a title, a date, and the words. There is
nothing to install and nothing to run.

Everything else in this repository is machinery for turning those letters into
a website. **The letters are the point. The machinery is disposable.** If the
machinery ever breaks, ignore it. The letters are unharmed.

## If you want a website, a book, or a PDF, just ask an AI assistant

You do not need to be a programmer to do anything with this repository.
Download the whole thing (the green **Code** button, then **Download ZIP**),
hand it to any capable AI assistant — Claude Code, or whatever exists in your
time — and say what you want in plain words.

For example, you can literally say:

> This repo contains letters from my father. Please turn them into a
> print-ready PDF book, ordered by date.

Or:

> Please rebuild the website from this repository so I can read it on my own
> computer.

The file `AGENTS.md` in this repository is written for that assistant. It tells
it how the letters are stored, how to rebuild the site, how to make a PDF or an
EPUB for a print shop, and — most importantly — that it must never reword,
shorten, or "improve" the letters.

## If the website has disappeared

The site normally lives at **letters.maglana.com**. If that address stops
working, the letters are still safe. Try each of these in order:

1. **The backup address:** <https://relaxdiego.github.io/letters>
2. **If that address sends you somewhere dead,** it is because this repository
   still contains a file pointing at the old custom domain. Delete the file
   `static/CNAME` here on GitHub, and clear the **Custom domain** box under
   **Settings → Pages**. The backup address will then serve the site directly.
3. **The Wayback Machine,** a public library of saved web pages:
   <https://web.archive.org/web/*/letters.maglana.com> — pick any date on the
   calendar to read the site as it was on that day.
4. **This repository itself:** <https://github.com/relaxdiego/letters> — the
   letters are the plain `.md` files in `content/letters/`, readable directly,
   or handed to an AI assistant as described above.
5. **Paper.** No printed copy exists yet. If someone in the family has since
   made one, ask around for it. If nobody has, please be the one who does.

The site's own [If this site disappears](https://letters.maglana.com/recovery/)
page says the same thing at more length, and appears in every Wayback Machine
snapshot.

---

## For developers

The site is [Hugo](https://gohugo.io), with no theme and no dependencies. The
layouts in `layouts/` are a few dozen lines of HTML with the CSS inlined.

Hugo is the only tool this project needs, and its version is pinned to
`0.163.3` in two places that must always agree:

- `HUGO_VERSION` — a plain text file, readable by anyone, forever.
- `devbox.json` + `devbox.lock` — the exact same version, resolved to an exact
  nixpkgs commit.

CI checks on every push that those two agree and that the binary is the
`extended` build. If they ever drift, the build fails rather than quietly
producing a site with a different Hugo.

### The easy way: devbox + direnv

With [devbox](https://www.jetify.com/devbox) and
[direnv](https://direnv.net) installed, `cd` into this directory and the right
Hugo appears on your `PATH` automatically. Nothing is installed globally and
nothing is left behind.

```
direnv allow      # once, the first time
hugo server       # open the address it prints
```

The first `cd` takes a minute while devbox fetches Hugo. After that it is
instant. GitHub Actions uses the same `devbox.lock`, so CI and your laptop
build with a bit-for-bit identical Hugo.

### The durable way: no tools at all

devbox, direnv, nix and jetify are conveniences, and one day they will be gone.
This project never depends on them:

1. Read `HUGO_VERSION` to see which version you need.
2. Download that exact **extended** build from Hugo's releases page,
   <https://github.com/gohugoio/hugo/releases> — every version Hugo has ever
   published is kept there, so an old pinned version can always be fetched,
   however many years from now.
3. Run `hugo server` in this directory, and open the address it prints.

If that ever stops working, delete `devbox.json`, `devbox.lock` and `.envrc`.
They are machinery. Nothing in `content/` will notice.

To publish, push to `main`. GitHub Actions builds the site, deploys it to
GitHub Pages, and then asks the Wayback Machine to save the homepage and any
letters the push changed. That last step is allowed to fail without failing the
deploy.

To add a letter, create a file in `content/letters/` named
`YYYY-MM-DD-short-slug.md`, give it a `title` and a `date`, write the letter in
plain Markdown, and push. That is the entire process. See `docs/for-future-me.md`.
