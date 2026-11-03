---
name: log-reading
description: Log reading on the reader's Siftivo shelves. Use when they say they started, finished or gave up on a book, want to add a book to their to-read list, give a book a star rating, say how far through a book they are, ask to take a book off their shelves, list books they have read, or name books they loved or disliked.
---

# Log reading

Every change here writes to the reader's own Siftivo shelves, so change only
what they asked to change.

## 1. Find the book

Call `search_books` with the title, plus the author if the reader gave one.
Use the `workId` from the result.

- If two results could both be the book, such as the same title by different
  authors, ask which one. Do not pick for them.
- If the title and author together find nothing, search the title alone
  before giving up.
- If nothing matches, say the book is not in the Siftivo catalog. Do not log a
  different book in its place.

## 2. See where it stands now

Call `get_my_book` with the `workId`. It returns the current shelf
(`readingStatus`), `rating` and `progress`, or `onShelf: false`. Use it to
tell the reader what changed, such as "moved from Reading to Finished".

## 3. Turn their words into fields

| The reader says | Field |
|---|---|
| want to read, add to my list, save it | `readingStatus: "NOT_STARTED"` |
| started, reading, picked up | `readingStatus: "READING"` |
| finished, read it, done | `readingStatus: "FINISHED"` |
| gave up, quit, did not finish | `readingStatus: "DNF"` |
| four stars, 4.5, three and a half | `rating`, 0.5 to 5 in half steps |
| a score out of ten, such as 8/10 | `rating` of half that, so 4 |
| halfway, 30 percent, page 150 of 300 | `progress`, a whole percent |
| keep it private, only me | `visibility: "ONLY_ME"` |
| a note in their own words | `notes`, exactly as they said it |

"Finished it, four and a half stars" is one call: `readingStatus: "FINISHED"`
and `rating: 4.5`.

## 4. Write it

Call `add_to_library` once per book, with the `workId` and only the fields
from step 3.

- Every field you send replaces what the reader had before. Never send
  `rating` or `notes` unless the reader asked for that one to change, or you
  wipe what they wrote.
- Leave out `readingStatus` to keep the book on its current shelf, for
  example when the reader only adds a rating.
- Leave out `progress` when they finish or have not started a book. The shelf
  sets it.

## 4b. Many books, or how they felt about them

For a list of books they have read ("I read these five this year"), or books
they loved or disliked, call `save_my_taste` once with all of them, up to 25.
It takes titles, so skip steps 1 and 2.

- `feeling` is `read`, `loved` or `disliked`.
- `why` is their own reason when they gave one, such as "the humor and the
  friendship" for "I loved it for the humor and the friendship".
- It adds books not yet on a shelf to Finished and never changes one already
  there. If they also gave a book a rating or a shelf, follow with
  `add_to_library` for that book alone.
- Tell them which titles came back in `unmatched`.

## 5. Take a book off

Use `remove_from_library` only when the reader asks to take a book off their
shelves entirely. It discards the shelf, progress and notes. Their rating
stays. Say that in your reply.

Moving a book to another shelf is `add_to_library` with a new
`readingStatus`, not a removal.

## 6. Reply

One line per book: title, its shelf, and its rating if it has one, with the
book's `url`. No more than that.

## When the account is not linked

The shelf tools need the reader's Siftivo account. An error from
`get_my_book`, `add_to_library`, `remove_from_library` or `save_my_taste`
that reads as a 401, "requires authorization", "requires re-authorization" or
"token expired" means the account is not linked in this app. Say so in one
sentence: connecting their Siftivo account saves their reading here and in
the Siftivo app. Do not retry
the call, and do not say it was saved. `search_books` and the other catalog
tools still work without an account.
