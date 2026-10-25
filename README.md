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
stays as it is.

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
writes to your shelves only when you ask it to log or save a book. Book links
in the replies go to siftivo.com. The plugin stores nothing itself and runs
no code on your computer.

Privacy policy: [siftivo.com/privacy](https://siftivo.com/privacy).
Terms: [siftivo.com/terms](https://siftivo.com/terms).
Help: [siftivo.com/help](https://siftivo.com/help) or hello@siftivo.com.
