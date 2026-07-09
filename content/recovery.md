---
title: If this site disappears
---

If you are reading this page, maybe nothing is wrong yet and you are only
curious. Good. Read it anyway, so that you know where things are.

Websites do not last. Domain names have to be paid for. Companies close.
Someday `letters.maglana.com` will stop answering, and I will not be around to
fix it. So I am writing down, plainly, every place these letters are kept and
how to get them back. You do not need to be a programmer. If any of this feels
difficult, hand this page to a capable AI assistant and ask it to walk you
through the step you are stuck on.

The letters exist in five places. Work down the list until one of them works.

## 1. The backup web addresses

Try these two, in order:

<https://relaxdiego.com/letters>

<https://relaxdiego.github.io/letters>

These are the same website, served by GitHub under my other names instead of
`letters.maglana.com`. Often, when one address dies, another is still alive.

## 2. If the backup addresses send you somewhere dead

Each of these addresses is really a signpost pointing at the next one. Today
the chain runs like this:

    relaxdiego.github.io/letters  →  relaxdiego.com/letters  →  letters.maglana.com

If a name near the end of that chain has died, the signposts before it will
still point at it, and you will land on nothing. The fix is to take the dead
signposts down. Each one is a setting on GitHub, not a file — you cannot fix
this by deleting anything from the repository.

You will need to be signed in to GitHub as me, or as someone I have given
access. Then:

1. Go to <https://github.com/relaxdiego/letters>. Click **Settings**, then
   **Pages** in the left sidebar. Under **Custom domain**, clear the box and
   save. This stops the site pointing at `letters.maglana.com`.
2. If `relaxdiego.com` is also dead, do the same again on a *different*
   repository: <https://github.com/relaxdiego/relaxdiego.github.io>. Settings,
   then Pages, then clear **Custom domain** and save. This stops everything
   pointing at `relaxdiego.com`.

That second step surprises people, so I will say it plainly: the redirect to
`relaxdiego.com` is controlled by that other repository, not by this one.
Clearing it here is not enough.

Wait a few minutes, then try <https://relaxdiego.github.io/letters> again. With
both custom domains cleared, GitHub serves the letters at that address
directly, and no expired domain name can get in the way.

## 3. The Wayback Machine

The Wayback Machine is a library that keeps copies of old web pages. Every
time I published a letter, a copy was sent there automatically. Go to:

<https://web.archive.org/web/*/letters.maglana.com>

You will see a calendar. Days with a coloured circle are days a copy was
saved. Click any circle, then click a time, and you will see the site as it
looked on that day. Click through the letters and read them there. The copies
are real pages, not summaries.

## 4. The repository itself

Everything lives at:

<https://github.com/relaxdiego/letters>

The website is only a coat of paint. **The letters themselves are the plain
`.md` files inside the `content/letters/` folder.** Click any one of them.
You will see the letter, in ordinary words, with a title and a date at the
top. No special program is needed to read them — they are just text. You can
download the whole thing at once with the green **Code** button, then
**Download ZIP**, and keep it somewhere safe.

If you want the website back, or a book, hand the whole repository to a
capable AI assistant and ask it plainly. Something like:

> This repository contains letters from my father. Please turn them into a
> print-ready PDF book, ordered by date.

Or:

> Please rebuild the website from this repository so I can read it on my own
> computer.

The file `AGENTS.md` in the repository tells that assistant everything it
needs to know. It is written for exactly this moment.

## 5. Paper

This step is the one I cannot promise you.

As I write this, no printed copy of these letters exists. I hope to make one.
Perhaps by the time you read this I will have, and there is a book on a shelf
somewhere in the family — so ask your mother, your brother, your cousins, your
aunts and uncles. It costs you nothing to ask.

But perhaps I never got around to it. If so, I am sorry, and I would ask you to
do the thing I did not. Print these letters. Have them bound. Keep one, and
give one to someone who does not live in your house, because houses burn down.
Paper needs no electricity, no password, and no company that still exists. It
is the only link in this chain that does not depend on the world staying the
way it is.

---

That is the whole chain. Four of those five places are real today, they are run
by different people in different countries, and losing the letters would take
all four failing at once.

The fifth is up to whoever reads this. It may be me. It may be you.

I would rather over-prepare than have you go looking for me one day and find
nothing there.

— Dad
