# Siftivo

Find your next book and keep track of what you read. Siftivo is a book
catalog with your own reading shelves. This plugin connects to it and adds
two skills, one for picking your next book and one for logging what you
read.

## Use it

**What to read next.** Ask "what should I read next?", name books you loved,
or describe a mood. With your Siftivo account connected, picks start from
your own taste. Each pick is checked against your shelves first, so a book
you have already read doesn't come back. Say which ones you want, and they
go on your want-to-read list.

**Log your reading.** Say "I just finished Piranesi, four and a half stars"
or "add Babel to my list". The book goes on the right shelf with your rating.
It changes only what you asked to change, so a note you wrote earlier
stays as it is. Name a few books you loved, and why, or list what you read
this year, and they are kept in your taste in one go. Ask it to post a review
in your words, and it goes on the book's page under your name.

**Keep a reading journal.** Say "journal for today: page 140 of Piranesi, the
tides scared me" or "about three hours left on the Project Hail Mary
audiobook". Each day gets one entry per book, with your note and where you got
to, by page for print or by time left for audio. Ask "where was I in The
Stand?" or "what did I write this week?" to read it back. The journal is
private.

You can also ask what a book is about, what order a series goes in, or what
everyone is reading this week. None of that needs an account.

## Your Siftivo account

Catalog lookups work without one. Your taste, your shelves and logging need
your Siftivo account. Connect it from the plugin's Connectors tab, and sign
in when asked. In Claude Code, run `/mcp`, pick siftivo, and sign in. You can
make a free account at [siftivo.com](https://siftivo.com).

## Data

The plugin talks to one server, `https://api.siftivo.com/mcp`, run by
Siftivo. It sends the book titles, author names and search words in your
request. With your account connected, it reads your taste and shelves. It
writes to your shelves, taste, journal and reviews only when you ask it to. A review is public. Book links
in the replies go to siftivo.com. The plugin stores nothing itself and runs
no code on your computer.

Privacy policy: [siftivo.com/privacy](https://siftivo.com/privacy).
Terms: [siftivo.com/terms](https://siftivo.com/terms).
Help: [siftivo.com/help](https://siftivo.com/help) or hello@siftivo.com.
