# Writing letters from a phone

This describes an iPhone Shortcut that creates a letter file in this
repository straight from the phone — either published immediately or saved
into `drafts/`. The Shortcut lives on the phone, not here. If it is lost or
broken, nothing in this repository is affected; this file is the recipe for
rebuilding it.

The idea is simple: publishing a letter means adding one Markdown file to
`content/letters/` on the `main` branch. The deploy workflow does the rest.
So the Shortcut asks for a title and the letter text, builds the file, and
creates it through GitHub's API with one HTTPS call.

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

Names below are the action names in the Shortcuts app.

1. **Choose from Menu** — two options: `Publish now` and `Save as draft`.
   - Under `Publish now`: **Set Variable** `Folder` to `content/letters`,
     and **Set Variable** `Prefix` to `feat(letters)`.
   - Under `Save as draft`: **Set Variable** `Folder` to `drafts`,
     and **Set Variable** `Prefix` to `chore(drafts)`.
2. **Ask for Input** — "Title?" (text).
3. **Ask for Input** — "The letter:" (text). Dictation works here; tap the
   microphone on the keyboard.
4. **Current Date**, then **Format Date** — custom format `yyyy-MM-dd`.
5. Build the slug from the title:
   - **Change Case** — lowercase.
   - **Replace Text** — regular expression ON, find `[^a-z0-9 ]`, replace
     with nothing (strips punctuation: "It's OK" becomes "its ok").
   - **Replace Text** — regular expression ON, find ` +`, replace with `-`.
6. **Text** — the whole file. This must match the letter format exactly:

   ```
   ---
   title: "<Ask for Input (title)>"
   date: <Formatted Date>T00:00:00+00:00
   ---

   <Ask for Input (letter)>
   ```

   (One caution: a title containing a `"` character will break the
   frontmatter. Leave double quotes out of titles.)
7. **Base64 Encode** — line breaks: **None**.
8. **Get Contents of URL**:
   - URL: `https://api.github.com/repos/relaxdiego/letters/contents/<Folder>/<Formatted Date>-<slug>.md`
   - Method: **PUT**
   - Headers:
     - `Authorization`: `Bearer <the token>`
     - `Accept`: `application/vnd.github+json`
   - Request Body: **JSON** with three text fields:
     - `message`: `<Prefix>: add <slug>`
     - `content`: the Base64-encoded text from step 7
     - `branch`: `main`
9. **Show Result** (or **Show Notification**) — so a failure is visible
   instead of silent.

A published letter is live on the site about two minutes later, once the
deploy workflow finishes.

## Publishing a draft later, from the phone

The Shortcut only creates files. To publish an existing draft, use github.com
in the phone's browser:

1. Open the file under `drafts/`, tap the pencil (edit).
2. In the filename field at the top, change the path from
   `drafts/<old-date>-<slug>.md` to `content/letters/<today>-<slug>.md`.
   Editing the path is how the web editor moves a file.
3. Update the `date:` line inside the file to the same day.
4. Commit directly to `main`.

## When it fails

- **HTTP 401** — the token expired. Make a new one (see above) and paste it
  into the Shortcut.
- **HTTP 422** — a file with that name already exists, usually from running
  the Shortcut twice with the same title on the same day. Nothing was
  overwritten; rename or edit on github.com instead.
- **Anything else** — github.com in the browser always works as the fallback:
  `content/letters/` → Add file → Create new file. The Shortcut is a
  convenience, never the only way in.
