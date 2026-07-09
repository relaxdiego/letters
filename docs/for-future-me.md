# For future me

Everything you need to remember about running this thing.

## Adding a letter

1. Create `content/letters/YYYY-MM-DD-short-slug.md`.
2. Frontmatter is `title` and `date`. Nothing else. Ever.
3. Write it in plain Markdown. No shortcodes.
4. Commit. Push.

That is the whole thing. GitHub Actions builds the site, deploys it, and pings
the Wayback Machine for the homepage and the letter you just added.

To see it first: `hugo server`. If you have devbox and direnv, `cd` into this
directory and Hugo is simply there. Otherwise `devbox run -- hugo server`, or
download the binary named in `HUGO_VERSION`.

Hugo's version is written in both `HUGO_VERSION` and `devbox.json`. They must
match; CI fails if they do not. When you bump Hugo, bump both, and remember
that `devbox search hugo` may lag a release or two behind Hugo's own downloads
page — pin what devbox can actually install.

The 44 letters in `content/letters/` were imported from the old WordPress blog
at `sinulatan.wordpress.com` (originally `letterstomikee.wordpress.com`), which
covered 2007 to 2015. Their frontmatter carries the original publication
timestamp, not just the date, because two pairs of letters share a day and the
time is what keeps them in order.

## The occasional things

**Print a physical volume. You have not done this yet.** It is the only step in
the recovery chain that does not exist, and the recovery page now says so
plainly, in your voice, to your sons. Fix that. `AGENTS.md` has the pandoc
recipe. Get it bound properly — not stapled. Give a copy to each son, and one to
someone outside the immediate household, because houses burn down. Paper is the
last link in the chain and the only one that does not depend on a company
staying in business. After the first volume exists, reprint every year or two,
or whenever there are enough new letters to justify it — and update the wording
in `content/recovery.md` and `README.md`, which currently both say no printed
copy exists.

**Keep the family map updated.** The document that says where everything is:
accounts, passwords, the domain registrar, where the physical copies went, who
to contact. These letters are worth nothing if nobody can find the door. Note
in that map that `letters.maglana.com` is registered, when it renews, and that
`AGENTS.md` in this repo explains how to un-break the site if the domain lapses.

**Check the domain renewal.** Two domains sit in front of this site:
`letters.maglana.com` on this repo, and `relaxdiego.com` on the separate
`relaxdiego.github.io` repo, which redirects every project page to itself. If
either lapses, follow the recovery steps in `AGENTS.md` — clear the custom
domain under Settings → Pages on whichever repo owns the dead name, then change
`baseURL` in `hugo.toml`. There is no `CNAME` file to delete; GitHub ignores
those when publishing from Actions.

**Check the archive occasionally.** Visit
<https://web.archive.org/web/*/letters.maglana.com> and confirm recent snapshots
exist. The workflow's archive step is allowed to fail silently, which means it
can fail silently for a long time without anyone noticing.

## Things not to do

Do not add a theme. Do not add analytics, comments, search, tags, or a
dark-mode toggle. Do not restructure the frontmatter. Do not make the letters
depend on Hugo to be readable.

Every one of these would be a small, sensible-seeming decision that makes the
letters slightly harder to recover in thirty years. The whole design of this
repository is the refusal to make them.
