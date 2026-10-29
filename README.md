# WeFishReviews for Cursor

Review movies and TV, and manage your watchlist and lists, from Cursor. This plugin connects Cursor to your [WeFishReviews](https://wefishreviews.onrender.com) account through the hosted MCP server at `https://wefishreviewsservice.onrender.com/mcp`.

Sign in with OAuth. Cursor registers itself (dynamic client registration is enabled on the server) and opens a WeFishReviews consent screen. No token is stored in this repository.

## What you can do

The plugin exposes your account tools:

| Tool | What it does |
| --- | --- |
| `search_media` | Find a movie or TV show by title. Returns the TMDB id, English title, year, poster, and WeFishReviews URL. |
| `get_media` | Read details, the community score, and your review if you have one. |
| `post_review` | Post a new review or update yours. Score is 0 to 10 (decimals allowed, for example 8.5). |
| `list_my_reviews` | Read reviews you have posted. |
| `delete_my_review` | Delete your review for one title. Requires an explicit confirmation. |
| `get_my_lists` | List every list you own, including private lists and the built-in Backlog. |
| `get_list_items` | Read a page of items from one of your lists. |
| `add_to_list` | Add a movie or TV show to a list or the Backlog. |
| `remove_from_list` | Remove a title from a list. Requires an explicit confirmation. |

"Watchlist" means the built-in Backlog. The names `backlog` and `watchlist` both match that list.

A short skill and rule ship with the plugin. They tell the assistant to use the 0–10 score scale (a rating out of 5 stars is doubled, so 4/5 = 8.0) and to confirm the exact title before posting or deleting a review.

## Example prompts

- "My review for Dune: 8/10. Huge scale, and the sound mix is half the movie."
- "Add Severance to my watchlist."
- "Pick a random movie from my Backlog."
- "What did I give The Bear?"
- "Delete my review for Morbius." (the assistant should ask you to confirm first)

## Connect with OAuth

This is the primary way to sign in.

1. Install **WeFishReviews** from the Cursor Marketplace after it is listed, or load this repo locally (see below).
2. Open **Customize** in the Cursor sidebar.
3. Find the WeFishReviews MCP server and choose **Connect**.
4. A browser window opens on WeFishReviews. Sign in with Google or email and password. Use a real account. Demo accounts cannot connect apps.
5. On the consent screen, approve the scopes you want (see below). You can leave write scopes off if you only want the assistant to read.
6. Return to Cursor. The server stays signed in as you. Cursor refreshes the access token when it expires.

Cursor's OAuth callbacks are `https://www.cursor.com/agents/mcp/oauth/callback` (web and Cloud Agents) and `http://localhost:8787/callback` (desktop). The server accepts those through dynamic client registration, so you do not create an OAuth app or paste a client id.

To disconnect, open your profile's [AI Assistance](https://wefishreviews.onrender.com/profile?tab=ai) tab and revoke the connected app. You can also sign out of the server from Customize.

### Scopes

The authorization server advertises these scopes. The consent screen lists them, and you choose which to grant.

| Scope | Allows |
| --- | --- |
| `reviews:read` | Search titles and read your reviews. |
| `reviews:write` | Post, update, and delete your reviews. |
| `lists:read` | Read your lists, including private lists and the Backlog. |
| `lists:write` | Add titles to your lists and remove them. |

Search, media details, your review diary, and list reads need the read scopes. Posting a review, deleting one, and editing a list need the matching write scope. Write tools also ask the assistant to confirm the title, and delete/remove tools require `confirm: true`.

## Personal token fallback

Use this only when OAuth Connect is not available. The plugin's `mcp.json` does not send a token. A personal token is a bearer token you create yourself and keep out of this repo.

1. Sign in at [https://wefishreviews.onrender.com](https://wefishreviews.onrender.com).
2. Open your profile and the **AI Assistance** tab: [https://wefishreviews.onrender.com/profile?tab=ai](https://wefishreviews.onrender.com/profile?tab=ai).
3. Generate a personal MCP token. Choose **Read & Write** if you want to post reviews and edit lists. A read-only token is enough to search, open titles, and read your reviews and lists.
4. Copy the token when it is shown. WeFishReviews stores only a SHA-256 hash of it. It cannot be shown again.
5. In Cursor, add a user-level MCP server (Customize → MCP, or `~/.cursor/mcp.json`) with the token in an `Authorization` header:

```json
{
  "mcpServers": {
    "wefishreviews": {
      "url": "https://wefishreviewsservice.onrender.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_PERSONAL_TOKEN"
      }
    }
  }
}
```

Replace `YOUR_PERSONAL_TOKEN` with the token from step 4. Do not commit that file, do not paste the token into chat, and do not send it to anyone. Revoke it from the same AI Assistance tab if it leaks. If the marketplace plugin is also installed, disable one of the two servers so you are not connected twice.

## Privacy

Connecting lets the assistant act as you on WeFishReviews.

- Read scopes let it see your reviews and your lists, including private lists and the Backlog.
- Write scopes let it publish and edit your reviews and change those lists. It should confirm the title with you before it writes, and it should not delete a review or remove a list item unless you agree.
- OAuth access tokens are short-lived. Refresh tokens rotate. Revoke the grant on the AI Assistance tab; a revoked grant stops working immediately.
- Personal tokens are stored only as a hash. The plaintext is shown once, at creation. Never share your password, and never put a token in this repository.
- The plugin sends no secret of its own. `mcp.json` contains only the public server URL.

## Support

On [https://wefishreviews.onrender.com](https://wefishreviews.onrender.com), use **Report a Bug** in the header or the footer. That opens the site's bug report form.

- AI Assistant guide: [https://wefishreviews.onrender.com/ai](https://wefishreviews.onrender.com/ai)
- FAQ: [https://wefishreviews.onrender.com/faq](https://wefishreviews.onrender.com/faq)
- Home: [https://wefishreviews.onrender.com](https://wefishreviews.onrender.com)

## Local install

Cursor loads a local plugin from `~/.cursor/plugins/local` when local plugin imports are allowed.

1. Copy this repository into `~/.cursor/plugins/local/wefishreviews` (the copy must live inside that folder).
2. Reload the window (**Developer: Reload Window**).
3. Open **Customize** and confirm the WeFishReviews server is listed, then use **Connect**.

Enterprise admins control local imports under Dashboard → Settings → Security & Identity → Marketplace and Plugins. A marketplace plugin with the same name takes precedence over the local copy.

## Develop

This repo is one plugin at the repository root (the template's single-plugin layout): `.cursor-plugin/plugin.json`, `mcp.json`, `rules/`, `skills/`, and `assets/logo.png`. The logo is the square site icon from [https://wefishreviews.onrender.com/icon-512.png](https://wefishreviews.onrender.com/icon-512.png).

Check the package with the template validator:

```bash
node scripts/validate-template.mjs
```

The script is the [cursor/plugin-template](https://github.com/cursor/plugin-template) validator, plus support for a plugin that lives at the repo root without `.cursor-plugin/marketplace.json`. Multi-plugin repos are still checked the same way.

## Marketplace

Do not submit this plugin from a pull request or an automated run. The maintainer submits the public repository at [https://cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) after the package is on `main`:

`https://github.com/PersonalAffairs/wefishreviews-plugin`
