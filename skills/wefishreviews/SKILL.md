---
name: wefishreviews
description: Review movies and TV on WeFishReviews and manage the Backlog and custom lists. Use when the user wants to rate a title, write or update a review, delete a review, search for a film or show, or add, remove, or pick something from their lists.
---

# WeFishReviews

The site is https://wefishreviews.onrender.com. Use only the signed-in user's tools: `search_media`, `get_media`, `post_review`, `list_my_reviews`, `delete_my_review`, `get_my_lists`, `get_list_items`, `add_to_list`, and `remove_from_list`.

## Score scale

- Scores are numbers from 0 to 10. Decimals are allowed, such as 8.5.
- If the user gives a rating out of 5 stars, convert it to the 10-point scale by doubling it. Example: 4/5 stars = 8.0.
- Do not invent a score. Ask when the user has not given one.

## Confirm the title before writing

1. If you do not already have a TMDB id, call `search_media` with the title. Use `type` `movie` or `tv` when the user was specific, otherwise `all`.
2. Tell the user the match you intend to use: English title, release year, and whether it is a movie or a TV show. If search returns several plausible matches, list them and ask which one.
3. Call `get_media` when you need the existing review or the community score before writing.
4. Call `post_review` only after the user has confirmed the title. Pass `id` and `media_type` when you know them. `score` and `text` are required. Set `spoiler` to true only when the user says the review contains spoilers.
5. `post_review` updates an existing review for that title. Say so if `get_media` shows one already.
6. If `post_review` returns candidate matches instead of saving, stop and ask the user which candidate to use. Do not guess.

## Lists and the Backlog

- The built-in watch queue is the Backlog. The names "backlog" and "watchlist" both refer to that list.
- `get_my_lists` returns every list the user owns, including private lists, with an id and item count.
- `get_list_items` reads one list. Pass a list id when you have it. A number is always an id. A name must match exactly, ignoring case. If several lists share that name, the tool returns candidates and does not choose one; call it again with an id.
- To pick a random title, read `total_pages` (and `total_items`) from `get_list_items`, choose a page from 1 through `total_pages`, then choose an index on that page. Page size defaults to 50 and is capped at 100.
- `add_to_list` needs `list`, `id`, and `media_type`. Confirm the title first when it was not already unambiguous.
- `remove_from_list` and `delete_my_review` do nothing unless `confirm` is true. Set that only after the user explicitly agrees to the removal or deletion, and name the title back to them first.

## Reading history

- `list_my_reviews` returns the signed-in user's reviews, newest first. `limit` maxes out at 50. Use `page` to go further.
- Quote the user's own score and text. Do not present another viewer's review as theirs.
