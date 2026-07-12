# Writing letters from a phone

This describes an iPhone Shortcut, named **Write A Letter**, that saves a
draft into this repository straight from the phone. It only ever creates
drafts — publishing a letter is a computer job (see "Publishing a draft
later" below); the name is a leftover from an earlier version that could also
publish directly. The Shortcut lives on the phone, not here. If it is lost or
broken, nothing in this repository is affected; this file is the recipe for
rebuilding it.

The idea is simple: one text box, dumped as-is into `drafts/`, on its own
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

## The Shortcut, action by action

Names below are the action names in the Shortcuts app, and the variable
names match the real Shortcut, so you can compare side by side.

1. **Ask for Input** — "The thought" (text). Dictation works here; tap the
   microphone on the keyboard.
2. **Text** — `Bearer <the token>`, the word `Bearer`, a space, then the
   token from the one-time setup above, pasted in once.
3. **Set Variable** `GithubBearerToken` = the Text above.
4. **Current Date**, then **Format Date** — Date Format **Custom**, Format
   String `yyyy-MM-dd-HHmm` (date and time, so two drafts in one day never
   collide).
5. **Text** — `drafts/<Formatted Date>.md`
6. **Set Variable** `Path` = the Text above.
7. **Text** — `chore(drafts): add <Formatted Date>`
8. **Set Variable** `CommitMessage` = the Text above.
9. **Set Variable** `LetterBody` = the input from step 1.
10. **Encode** `LetterBody` **with base64**.
11. **Get Contents of URL** —
    `https://api.github.com/repos/relaxdiego/letters/branches/main`, Method
    **GET**, Headers `Authorization: <GithubBearerToken>`,
    `Accept: application/vnd.github+json`. This asks GitHub where `main`
    currently points.
12. **Get Value** for `commit.sha` **in** the Contents of URL above — GitHub's
    reply nests the commit under a `commit` key, so this reaches straight
    into it for the hash.
13. **Set Variable** `BaseSHA` = that Dictionary Value.
14. **Text** — `drafts/<Formatted Date>` (no `.md` — this one names a
    branch, not a file).
15. **Set Variable** `DraftBranchName` = the Text above.
16. **Get Contents of URL** —
    `https://api.github.com/repos/relaxdiego/letters/git/refs`, Method
    **POST**, Headers `Authorization: <GithubBearerToken>`,
    `Accept: application/vnd.github+json`, Request Body **JSON**: `ref` =
    `refs/heads/<DraftBranchName>`, `sha` = `<BaseSHA>`. GitHub does not
    create a branch just because you push a file to it — this is the step
    that actually creates `<DraftBranchName>`, pointed at the same commit as
    `main`.
17. **Get Contents of URL** —
    `https://api.github.com/repos/relaxdiego/letters/contents/<Path>`,
    Method **PUT**, same headers as step 16, Request Body **JSON**:
    `message` = `<CommitMessage>`, `content` = the Base64 Encoded text from
    step 10, `branch` = `<DraftBranchName>`. This is the step that creates
    the file — on the new branch, never on `main`.
18. **Set Variable** `GithubResponse` = the Contents of URL above.
19. **Get Value** for `content` **in** `GithubResponse`, then, from that,
    **Get Value** for `html_url` and separately for `sha` — pulled out only
    to show they worked.
20. **Text** — a short receipt:

    ```
    ✅ Draft saved to GitHub

    📁 Path: <Path>
    💬 Message: <CommitMessage>

    🔗 View on GitHub: <html_url>
    🔑 File SHA: <sha>
    ```
21. **Show** that Text — so a failure is visible instead of silent.

The draft just sits on its own branch, invisible to the site and to `main`.

## Publishing a draft later

That is a computer job, on purpose. A draft is raw text; a letter needs the
frontmatter and the dated file name. See "Drafts" in `AGENTS.md` for how to
turn one into a letter. An AI assistant can do the formatting — that same
note tells it to format the words, never reword them.

## When it fails

- **HTTP 401** — the token expired. Make a new one (see above) and paste it
  into the Shortcut.
- **HTTP 422** — the branch or file already exists, usually from running the
  Shortcut twice in the same minute. Nothing was overwritten; wait a minute
  and try again, or rename the branch on github.com instead.
- **Anything else** — github.com in the browser always works as the fallback:
  `content/letters/` → Add file → Create new file. The Shortcut is a
  convenience, never the only way in.
