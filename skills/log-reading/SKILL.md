---
name: log-reading
description: Log reading on the reader's Siftivo shelves. Use when they say they started, finished or gave up on a book, want to add a book to their to-read list, give a book a star rating, ask to take a book off their shelves, list books they have read, name books they loved or disliked, want to post, change, read or delete a review, or want to write, read or delete a reading journal entry, such as a day's note, the page they reached, or the time left on an audiobook.
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
(`readingStatus`), `rating` and percent read (`progress`), or `onShelf: false`. Use it to
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
| finished it on Sunday, started it last week | `date`, YYYY-MM-DD in their time zone |
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
- Where they are in a book (page 150, two hours left) is the journal, not
  this tool. See step 6.
- A rating alone on a book not on a shelf saves the rating and shelves nothing.
  Say so if the reader asks where the book is.

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

## 6. The reading journal

One entry per book per day: a note in their words, where they got to, or
both. Notes are private. Call `write_journal` with `entries`, one per book,
and skip steps 1 and 2: it takes titles and matches the books they are
reading now first.

- `date` is the reader's own today, or the day they name ("yesterday"), as
  YYYY-MM-DD.
- Print or ebook: `page`, with `totalPages` if they say how long their copy is.
- Audio: `minutesLeft`. "Three hours left" is 180. "Four hours in" is
  `totalMinutes` minus 240, so ask for the runtime if you do not know it.
- A Kindle percent goes in the note, in their words. There is no percent field.
- "I'm listening to this one now" with no position is `trackBy: "time"`.
- On `unit_differs`, the book is tracked the other way. Ask whether to switch,
  and resend with `switchUnit: true` only if they say yes.
- A note on a day that already has one is added under it. Send
  `replaceNote: true` only when they ask to replace what they wrote.
- On `ambiguous`, ask which of the `candidates` they mean.
- Finishing a book is `add_to_library` with `readingStatus: "FINISHED"`, not
  a journal entry.

`get_my_journal` with their `today` reads it back: the books they are
reading, each with `trackBy`, the last page or `minutesLeft`, and whether
today is logged. With `book`, it returns that book's days and notes, for
"where was I in X" and "what did I write about X". `delete_journal_entry`
deletes one day for one book, so use it only when they ask.

## 7. Reviews

A review is public: it goes on the book's page under the reader's name and to
their followers. `notes` is the private one. Use `write_review` only when the
reader asks to post a review.

- `reviewText` is the reader's own words. If they ask for help, show them a
  draft and post only what they approve, word for word.
- Never add words to reach the 50 character minimum. On `too_short`, ask them
  for more.
- `rating` is half stars. On `needs_rating`, ask them for one.
- On `exists`, show them the review that is already there and ask whether to
  replace it. Send `replaceExisting: true` only if they say yes.
- Set `containsSpoilers` when they say it gives away the plot.
- If `public` comes back false, say the review reaches nobody else.

`get_my_book` returns their review, for "what did I write about X".
`delete_review` takes one down for good and leaves the rating, so use it only
when they ask.

## 8. Reply

One line per book: title, its shelf, and its rating if it has one, with the
book's `url`. No more than that.

## When the account is not linked

The shelf tools need the reader's Siftivo account. An error from
`get_my_book`, `add_to_library`, `remove_from_library`, `save_my_taste`,
`write_review`, `delete_review` or the journal tools that reads as a 401, "requires authorization", "requires re-authorization" or
"token expired" means the account is not linked in this app. Say so in one
sentence: connecting their Siftivo account saves their reading here and in
the Siftivo app. Do not retry
the call, and do not say it was saved. `search_books` and the other catalog
tools still work without an account.
