---
name: what-to-read-next
description: Recommend books for the reader to read next. Use when they ask what to read next, want a book recommendation or "something I would like", name books they loved and want more like them, or describe a mood, theme or subject they want to read about.
---

# What to read next

Picks come from the Siftivo catalog, and for a reader with a linked Siftivo
account, from their own taste and shelves. Every book you suggest is one the
reader has not read yet.

## 1. Work out what the reader gave you

- **Books they loved** ("I loved Circe and Piranesi"): go to step 3.
- **A mood, theme or subject** ("something cozy", "a good book about grief"):
  go to step 4.
- **Nothing**, just "what should I read next?": go to step 2.

If they said fiction or nonfiction, pass `isFiction` (true or false) to every
recommend call. If they named both books and a mood, run steps 3 and 4 and
merge the results.

## 2. Start from their taste

Call `get_my_taste` with no arguments. It returns the strongest 40 entries.

**If it works**, read `steering`:

- Entries with `direction: "toward"` are what the reader likes. Entries with
  `direction: "away"`, and every name in `muted`, are what to avoid. Never
  suggest those, or books close to them.
- Take up to five `toward` entries with `kind: "work"`, strongest first, and
  call `recommend_from_titles` with each as `"Title by Author"` (the `name`
  and `byline` fields). If those books are a mix of fiction and nonfiction,
  make one call for each, with `isFiction` set.
- If there are `toward` entries with `kind` `genre`, `topic` or `vibe`, also
  call `recommend_from_description` with a short phrase built from two or
  three of them, such as "slow, atmospheric literary fantasy".
- `discoveryStyle` says how far from their usual taste they like to go.
  `SAFE` means stay close to what they already love. `ADVENTUROUS` means
  include one pick that is further afield. `BALANCED` needs nothing extra.

**If it fails**, the reader has not linked their Siftivo account in this app.
The error can read as a 401, "requires authorization", "requires
re-authorization" or "token expired". All of them mean the same thing. The
catalog tools in steps 3 and 4 still work without an account, so keep going
with Siftivo. Do not fall back to picks from memory.

Say once, in one sentence, that connecting their Siftivo account lets picks
come from their own shelves and skip what they have read. Then ask one
question: two or three books they loved, or the mood they are in. When they
answer, go to step 3 or 4.

## 3. From books they loved

Call `recommend_from_titles` with the titles, as `"Title by Author"` when you
know the author. Up to 10 titles.

- Anything in `unmatched` is not in the catalog. Tell the reader which titles
  were not found. Do not swap in a guess.
- If every title is unmatched, try `search_books` on each one in case of a
  spelling difference, then call `recommend_from_titles` again with the titles
  it finds.
- Other books by the same authors are left out on purpose. If the reader
  wants more from one author, use `get_author`. If they want the next book in
  a series, use `what_to_read_next` with the book's `workId`.

## 4. From a mood or theme

Call `recommend_from_description` with the reader's own words, under 300
characters. Keep their phrasing. Do not rewrite it into genre labels.

## 5. Check every pick against their shelves

Gather every book you plan to suggest. That includes books you thought of
yourself and did not get from a tool. For a book of your own, call
`search_books` first so you have its `workId` and know it is in the catalog.

If the reader's account is linked, call `get_my_book` once with all the
picks, up to 25, as `workId` values. Drop every book with `onShelf: true` or
a `rating`, because the reader has already read or rated it. The recommend
tools already leave out shelved books for a linked reader, so this check
matters most for the books you added yourself.

If `get_my_taste` already failed, the account is not linked, so skip this
step. If `get_my_book` fails the same way, skip the check and say once that
the picks were not checked against their shelves.

## 6. Show three to five picks

For each book:

- Title and author.
- One sentence on why it fits. Use `why.reason` from the result when it is
  there ("Because you liked The Obstacle is the Way"). Otherwise tie it to
  the book or mood the reader named.
- The `url` from the result, exactly as given.

Do not show `score`, `explanation` or match percentages. Do not read the
taste profile back to the reader. Keep it short.

## 7. Offer to save them

If the account is linked, ask whether they want any of the picks on their
want-to-read list. Save only the books they choose. For each one, call
`add_to_library` with the `workId` and `readingStatus: "NOT_STARTED"`, and no
other field. Then confirm in one line.

Never save a book the reader did not ask for. If the account is not linked,
the link on each book is enough.
