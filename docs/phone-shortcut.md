# Writing letters from a phone

This describes an iPhone Shortcut that creates a letter file in this
repository straight from the phone — either published immediately or saved
into `drafts/`. The Shortcut lives on the phone, not here. If it is lost or
broken, nothing in this repository is affected; this file is the recipe for
rebuilding it.

The idea is simple: publishing a letter means adding one Markdown file to
`content/letters/` on the `main` branch. The deploy workflow does the rest.
So the Shortcut builds a file and creates it through GitHub's API with one
HTTPS call. Publishing asks for a title and the letter text and formats them
properly; a draft is free-form — one text box, dumped as-is into `drafts/`
with a timestamp for a name, cleaned up later at a computer.

## One-time setup: a GitHub token

The Shortcut needs permission to write to this repository.

1. On github.com: **Settings → Developer settings → Personal access tokens →
   Fine-grained tokens → Generate new token.**
2. Repository access: **Only select repositories** → `relaxdiego/letters`.
3. Permissions: **Contents → Read and write.** Nothing else.
4. Expiration: pick the longest allowed and **set a calendar reminder** for a
   week before it expires. When the token dies, the Shortcut fails with
   HTTP 401 and nothing tells you in advance.

The token is pasted into the Shortcut in plain text. It is scoped to this one
repository, so the worst it can leak is the ability to edit this repo — but
treat the phone's lock screen as the only wall around it.

## The Shortcut, action by action

Names below are the action names in the Shortcuts app. The shape: a menu
whose two branches each fill three variables — `Path`, `Message`, `File` —
and a shared tail that uploads whatever the branch prepared.

1. **Choose from Menu** — two options: `Save as draft` and `Publish now`.

   Under **Save as draft** (the short path):
   - **Ask for Input** — "The thought:" (text). Dictation works here; tap
     the microphone on the keyboard.
   - **Current Date**, then **Format Date** — custom format
     `yyyy-MM-dd-HHmm` (date and time, so two drafts in one day never
     collide).
   - **Set Variable** `Path` = `drafts/<Formatted Date>.md`
   - **Set Variable** `Message` = `chore(drafts): add <Formatted Date>`
   - **Set Variable** `File` = the input, exactly as typed. No frontmatter,
     no title, no cleanup — that happens later, at a computer.

   Under **Publish now**:
   - **Ask for Input** — "Title?" (text).
   - **Ask for Input** — "The letter:" (text).
   - **Current Date**, then **Format Date** — custom format `yyyy-MM-dd`.
   - Build the slug from the title:
     - **Change Case** — lowercase.
     - **Replace Text** — regular expression ON, find `[^a-z0-9 ]`, replace
       with nothing (strips punctuation: "It's OK" becomes "its ok").
     - **Replace Text** — regular expression ON, find ` +`, replace with `-`.
   - **Text** — the whole file. This must match the letter format exactly:

     ```
     ---
     title: "<Ask for Input (title)>"
     date: <Formatted Date>T00:00:00+00:00
     ---

     <Ask for Input (letter)>
     ```

     (One caution: a title containing a `"` character will break the
     frontmatter. Leave double quotes out of titles.)
   - **Set Variable** `Path` = `content/letters/<Formatted Date>-<slug>.md`
   - **Set Variable** `Message` = `feat(letters): add <slug>`
   - **Set Variable** `File` = the Text above.

2. After the menu, the shared tail:
   - **Base64 Encode** `File` — line breaks: **None**.
   - **Get Contents of URL**:
     - URL: `https://api.github.com/repos/relaxdiego/letters/contents/<Path>`
     - Method: **PUT**
     - Headers:
       - `Authorization`: `Bearer <the token>`
       - `Accept`: `application/vnd.github+json`
     - Request Body: **JSON** with three text fields:
       - `message`: `<Message>`
       - `content`: the Base64-encoded text
       - `branch`: `main`
   - **Show Result** (or **Show Notification**) — so a failure is visible
     instead of silent.

A published letter is live on the site about two minutes later, once the
deploy workflow finishes. A draft just sits in `drafts/`, invisible to the
site.

## Publishing a draft later

That is a computer job, on purpose. A draft is raw text; a letter needs the
frontmatter and the dated file name. At the computer: create a proper file in
`content/letters/YYYY-MM-DD-short-slug.md` (copy any finished letter for the
shape), dated the day you publish, paste the words in, and delete the draft
file. An AI assistant can do the formatting — the note in `AGENTS.md` tells
it to format the words, never reword them.

## When it fails

- **HTTP 401** — the token expired. Make a new one (see above) and paste it
  into the Shortcut.
- **HTTP 422** — a file with that name already exists, usually from running
  the Shortcut twice with the same title on the same day. Nothing was
  overwritten; rename or edit on github.com instead.
- **Anything else** — github.com in the browser always works as the fallback:
  `content/letters/` → Add file → Create new file. The Shortcut is a
  convenience, never the only way in.
