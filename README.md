# obsidian-link

A static redirect page that turns a regular `https://` link into an `obsidian://` link, so **apps that don't support custom URL schemes** (such as Telegram) can open a specific Obsidian note with a single tap.

## Why

The Telegram Bot API doesn't accept `obsidian://` links:

- Inline keyboard buttons are rejected with `Unsupported URL protocol`
- `<a href="obsidian://…">` in message text is stripped
- A bare `obsidian://…` in plain text isn't turned into a clickable link

So you send a regular `https://` link instead. It opens this page, and the page redirects to Obsidian.

## Usage

```
https://<github-id>.github.io/obsidian-link/#vault=<vault name>&file=<note path>
```

| Parameter | Description |
|---|---|
| `vault` | Name of the Obsidian vault |
| `file` | Path of the note inside the vault, without `.md` |

Both parameters must be URL-encoded (spaces, non-ASCII characters and `/` included).

**Example**: open `Notes/Daily/2026-01-01` in the vault `MyVault`

```
https://menglin31.github.io/obsidian-link/#vault=MyVault&file=Notes%2FDaily%2F2026-01-01
```

### Building links in code (Python)

```python
from urllib.parse import urlencode, quote

def obsidian_link(vault: str, file: str) -> str:
    return "https://menglin31.github.io/obsidian-link/#" + urlencode(
        {"vault": vault, "file": file}, quote_via=quote)
```

### Using it in a Telegram inline button

```python
reply_markup = {"inline_keyboard": [[
    {"text": "📖 Open note", "url": obsidian_link("MyVault", "Notes/Daily/2026-01-01")}
]]}
```

## What happens when you tap it (iPhone)

1. Telegram opens this page in its in-app browser
2. The page redirects automatically, and iOS asks "Open in Obsidian?"
3. Tap "Open" and Obsidian opens the note

If the redirect doesn't happen automatically, tap the "打開 Obsidian" (Open Obsidian) button on the page.

## Privacy and security

- **Note paths never reach the server.** The parameters live after the `#` in the URL. Browsers don't send anything after `#` to GitHub; it's only read on your device.
- **The page contains no personal information.** No vault name or folder is hardcoded; it's a general-purpose tool.
- **It only redirects to Obsidian.** The script only builds `obsidian://open` links, so it can't be used to redirect to other websites.
- No external scripts, fonts or analytics are loaded. `noindex` keeps search engines from indexing it, and `no-referrer` keeps the source URL from being sent to other sites.

## Files

| File | Description |
|---|---|
| `index.html` | The entire redirect page (HTML plus a small piece of JavaScript), no dependencies |

## Deployment

Use GitHub Pages: Settings → Pages → Source: `Deploy from a branch`, Branch: `main`, `/ (root)`.
