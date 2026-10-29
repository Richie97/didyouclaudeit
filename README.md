# Did you Claude it?

A "let me Google that for you" for Claude. Type the question someone asked you
instead of asking Claude, send them the link, and they watch it get typed out
before landing in Claude with the question already filled in.

Live at https://didyouclaudeit.com.

## How it works

It is one static file, `index.html`, with no build step and no backend. The
prompt lives in the link's query string, so nothing is stored anywhere.

- `/` with no `q` shows the link generator.
- `/?q=<prompt>` plays the animation, then sends the visitor on.

| Parameter | Meaning |
| --- | --- |
| `q` | The prompt. Required for the player. |
| `to` | Where to send them: `claude` (default), `desktop`, `code`, `codex`, `chatgpt`. Unknown values fall back to `claude`. |
| `repo` | Claude Code only: a GitHub `owner/name` to open their local clone of. |

Every destination only pre-fills the prompt. None of them sends it, so the
recipient always reads it and presses Enter themselves.

| `to` | Opens | Notes |
| --- | --- | --- |
| `claude` | `https://claude.ai/new?q=` | Works for anyone. Auto-redirects after a 5 s countdown. |
| `desktop` | `claude://claude.ai/new?q=` | Claude Desktop. Prompt truncated around 14,000 characters. |
| `code` | `claude-cli://open?q=&repo=` | Claude Code in a terminal. 5,000 characters max. The handler is only registered after the user has sent one prompt in an interactive Claude Code session on that machine. |
| `codex` | `codex://new?prompt=` | Codex app. |
| `chatgpt` | `https://chatgpt.com/?q=` | Auto-redirects like `claude`. |

The app targets (`desktop`, `code`, `codex`) use custom URL schemes. A browser
can't tell whether one is installed, and some show an error for an unhandled
one, so the player never auto-redirects to them: it waits for a click and
offers the web version as a fallback.

Docs for the formats:
[Claude Code deep links](https://code.claude.com/docs/en/deep-links),
[Claude Desktop links](https://support.claude.com/en/articles/14729294-open-claude-desktop-with-a-link),
[Codex app commands](https://developers.openai.com/codex/app/commands).

## Hosting

### GitHub Pages (simplest)

1. Repo **Settings → Pages → Build and deployment**: Source *Deploy from a
   branch*, branch `main`, folder `/ (root)`.
2. The `CNAME` file already names `didyouclaudeit.com`. At your DNS provider,
   point the apex at GitHub Pages with four `A` records (`185.199.108.153`,
   `185.199.109.153`, `185.199.110.153`, `185.199.111.153`) and add a `CNAME`
   for `www` to `richie97.github.io`.
3. Once the certificate is issued, tick **Enforce HTTPS**.

`.nojekyll` stops Pages from running Jekyll over the repo.

### Cloudflare Pages (alternative)

Create a Pages project from this repo with no build command and `/` as the
output directory, then add `didyouclaudeit.com` under **Custom domains**. If
the domain's DNS is already on Cloudflare, this is the fewer-clicks option.
The `CNAME` file is ignored there.

## Local preview

```sh
python3 -m http.server 8000
```

Then open http://localhost:8000.
