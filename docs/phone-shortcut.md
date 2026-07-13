# Writing letters from a phone

This describes an iPhone Shortcut, named **Write A Letter**, that saves a
draft into this repository straight from the phone. It only ever creates
drafts — publishing a letter is a computer job (see "Publishing a draft
later" below); the name is a leftover from an earlier version that could also
publish directly. The Shortcut lives on the phone, not here. If it is lost or
broken, nothing in this repository is affected; this file is the recipe for
rebuilding it.

The idea is simple: one text box, dumped as-is into `drafts/` on the `drafts`
branch (see "Drafts" in `AGENTS.md`), cleaned up later at a computer. The
Shortcut builds that file and creates it through GitHub's API.

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

## One-time setup: the `drafts` branch

Every draft is committed to a single long-lived branch named `drafts`. The
Shortcut does not create it — it expects it to be there. It was created once,
from `main`, and it stays forever. Nothing needs doing unless somebody deletes
it, in which case: `git switch -c drafts main && git push -u origin drafts`.

## The Shortcut, action by action

Names below are the action names in the Shortcuts app, and the variable
names match the real Shortcut, so you can compare side by side.

1. **Ask for Input** — "The thought" (text). Dictation works here; tap the
   microphone on the keyboard.
2. **Copy to Clipboard** — the input from step 1. This is a safety net, not a
   feature: if any later step fails, the words are still on the clipboard and
   can be pasted somewhere safe instead of being lost with the error message.
3. **Set Variable** `LetterBody` = the input from step 1.

Steps 4 to 9 invent the slug — the few words in the file name that say what
the draft is about, so the folder can be read at a glance. They are a
convenience. If they ever break, the fix is to delete them and put the plain
timestamp back in the file name; nothing else depends on them.

4. **Use Model** — Model **On-Device**, Output **Text**. Apple's own model,
   running on the phone; it needs no network and no account. The prompt, with
   `LetterBody` inserted at the end:

   ```
   Below is a rough note. Name what it is about in 3 to 5 words, as a plain
   phrase, the way you would finish the sentence "this is about ___".

   Do not list keywords or themes. Write one natural phrase.
   Reply with only that phrase, lowercase, words separated by hyphens.
   No punctuation. No quotes. No explanation.

   Example note: "- being with other people is not always about being
   intellectually stimulating - sometimes it's just to feel connected, safe,
   loved"
   Example reply: being-with-other-people

   Note:
   ```

5. **Change Case** — **lowercase**, on the model's answer.
6. **Replace Text** — four of them, each feeding the next, each with
   **Regular Expression** switched **on**. A model will hand back a stray
   period or a curly quote however firmly you ask it not to; these scrub the
   answer down to letters, numbers and hyphens:

   | Find | Replace with | Why |
   | --- | --- | --- |
   | `\s+` | `-` | spaces become hyphens |
   | `[^a-z0-9-]` | *(empty)* | drop punctuation, quotes, emoji, accents |
   | `-{2,}` | `-` | collapse repeated hyphens |
   | `^-+\|-+$` | *(empty)* | trim hyphens off both ends |

7. **Set Variable** `Slug` = the result.
8. **Current Date**, then **Format Date** — Date Format **Custom**, Format
   String `yyyy-MM-dd-HHmm` (date and time, so two drafts in one day never
   collide).
9. **Text** — `drafts/<Formatted Date>-<Slug>.md`
10. **Set Variable** `Path` = the Text above.
11. **Text** — `chore(drafts): add <Formatted Date>` — the slug is left out of
    the commit message on purpose, so the message can never grow too long.
12. **Set Variable** `CommitMessage` = the Text above.
13. **Text** — `Bearer <the token>`, the word `Bearer`, a space, then the
    token from the one-time setup above, pasted in once.
14. **Set Variable** `GithubBearerToken` = the Text above.
15. **Encode** `LetterBody` **with base64**.
16. **Get Contents of URL** —
    `https://api.github.com/repos/relaxdiego/letters/contents/<Path>`,
    Method **PUT**, Headers `Authorization: <GithubBearerToken>`,
    `Accept: application/vnd.github+json`, Request Body **JSON**:
    `message` = `<CommitMessage>`, `content` = the Base64 Encoded text from
    step 15, `branch` = `drafts`. This one call creates the file, commits it,
    and pushes it — on the `drafts` branch, never on `main`.
17. **Set Variable** `GithubResponse` = the Contents of URL above.
18. **Get Value** for `content` **in** `GithubResponse`, then, from that,
    **Get Value** for `html_url` and separately for `sha` — pulled out only
    to show they worked.
19. **Text** — a short receipt:

    ```
    ✅ Draft saved to GitHub

    📁 Path: <Path>
    💬 Message: <CommitMessage>

    🔗 View on GitHub: <html_url>
    🔑 File SHA: <sha>
    ```
20. **Show** that Text — so a failure is visible instead of silent.

The draft sits on the `drafts` branch, invisible to the site and to `main`.

## Reading the drafts on a computer

```
git fetch && git switch drafts
```

They are all in `drafts/`, in one place, named by date and topic.

## Publishing a draft later

That is a computer job, on purpose. A draft is raw text; a letter needs the
frontmatter and the dated file name. See "Drafts" in `AGENTS.md` for how to
turn one into a letter. An AI assistant can do the formatting — that same
note tells it to format the words, never reword them.

## When it fails

- **HTTP 401** — the token expired. Make a new one (see above) and paste it
  into the Shortcut.
- **HTTP 404 or 422 mentioning the branch** — the `drafts` branch is gone.
  Recreate it (see the one-time setup above).
- **HTTP 422** — the file already exists: the same minute and the same slug,
  so almost certainly the Shortcut run twice on one thought. Nothing was
  overwritten. Wait a minute and try again.
- **The Use Model action errors** — Apple Intelligence is unavailable, or a
  future iOS renamed the action. The thought is on the clipboard (step 2), so
  paste it somewhere before you do anything else. Then either fix that action
  or delete steps 4 to 7 and use `drafts/<Formatted Date>.md` as the path.
- **Anything else** — github.com in the browser always works as the fallback:
  the `drafts` branch → `drafts/` → Add file → Create new file. The Shortcut
  is a convenience, never the only way in.
