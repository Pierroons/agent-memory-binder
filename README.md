# An organized memory for Claude Code

*[Français](README.fr.md)*

Claude Code forgets everything from one session to the next. It does have a built-in memory: a
folder of notes, and an index it reads again at every start. Without rules, that folder soon turns
into a messy drawer: duplicate notes, outdated facts, an index too long to be read in full.

And as it works, Claude leaves files everywhere: one-off scripts, downloads, drafts, in the middle
of your projects.

This repository gives a method to tidy both: its memory, and the desk around it. You install nothing
yourself: you hand one file to your Claude Code, and it sets everything up, showing you each step
before doing it.

## The idea in one picture: a binder

- **The table of contents** is always open. Each line says *when* to take a note out: "before
  publishing → read the publishing note".
- **The notes** stay filed. Claude takes one out when the table of contents tells it to.
- **The archive**: a finished project goes to the attic. The table of contents keeps one line so
  Claude knows it exists.
- **The "to file" tray**: a decision made today waits there to be copied into its note, with a
  deadline.
- **The desk**: a drafts tray that empties itself after the delay you choose, and a drawer for
  finished documents, which never empties.

If you run several Claude sessions at the same time (one on the website, one on the server, for
example), each of them also gets **a mailbox**: a file where the others leave it messages. A
**board** says who is working on what right now, so that two sessions do not fix the same thing.
And when they share a git branch, they publish through **one script that you run**: before sending,
it takes in what the others have just sent, then logs the send. Over its first four days with us,
from 29 September to 2 October 2026, it carried 22 sends, all logged; twice, two sessions sent less
than a minute apart, and both sends went through. And if they run on several machines, the binder
tells how to connect them through Claude Code's bridge, and how to make sure you write to the right one.

## What it gives you

- Claude picks up where it left off, without you explaining again.
- A rule you gave once stays given.
- When a fact changes, it changes in one place.
- No more files scattered through your projects: drafts have their tray, finished documents their
  drawer.
- You can read what it knows yourself: these are text files.
- Its checks are proven: a check counts only once you have seen it fail, so the checker plants
  defects on purpose and verifies that it reports each one.

## What it does not do

- **No token savings on the first request.** The measurement is right below.
- **A memory keeps false things as well as true ones.** The method includes checks; it does not
  make Claude infallible.
- **It needs some upkeep.** For us, that is one review every two weeks.

## What it costs, measured

Measured on 26 September 2026, on a memory of about 200 notes, with and without the memory: 15
questions asked three times each, then three real tasks corrected until the answer was complete.

- **It costs** about 8,000 more tokens every time Claude thinks or uses a tool, because the table of
  contents is read again each time. Those tokens come mostly from the cache, so they cost little.
- **It brings** the answers that exist only in the memory: 15 right out of 15 with it, 2 out of 14
  without. Without it, Claude searches everywhere and finds nothing, and each right answer costs it
  more than ten times as much ($1.24 against $0.10, estimated at list price).
- **It cuts corrections**: over the three tasks, 9 were needed with the memory against 15 without.
  Depending on the task, the tokens needed to reach a complete answer range from 41% fewer to 36%
  more.
- **It does not shorten** the search when the answer lies elsewhere in your files.

In short: the memory does not reduce the tokens of a first answer, it adds some at every call. What
it reduces is how often you have to correct. Next measurement: with a translator, a step that turns
your request into clear instructions from the start, compare with and without the memory. It will
tell whether a memory already in place uses less than an instruction passed on every time.

To see what your own memory costs, type `/context` at the beginning of a session. The full protocol
is in [`MEASURE.md`](MEASURE.md), so you can run the measurement yourself.

## Install

1. Download [`SETUP-FOR-YOUR-AGENT.md`](SETUP-FOR-YOUR-AGENT.md) into your project.
2. Open Claude Code in that project and tell it: "Read `SETUP-FOR-YOUR-AGENT.md` and set this
   system up for me."
3. It asks you five questions, then shows you what it will create before creating it.

## What stays your decision

- **Deleting a note.** Claude shows it to you first, and you say yes.
- **Publishing anything.** When sessions share a git branch, you run the send script yourself, and
  a hook keeps Claude from pushing.
- **Your memory folder never goes to a public repository.** It holds what Claude knows about you,
  your projects and your machines.

## Questions

**Do I need Obsidian?** No. Everything is plain text (Markdown). Obsidian is handy to browse the
archive, nothing more.

**Does it work with an agent other than Claude Code?** The idea does: it is files and rules. The
paths and the automatic reminder at startup are specific to Claude Code, so you will have to adapt
them.

**What does it cost?** The index is read at every session, so it costs every time: that is why it
stays short. A note costs only when it is read.

**Where does it come from?** From daily use: about 200 notes, and nine Claude sessions working in
parallel on the same projects. Every rule comes from a problem we ran into.

## License

[CC-BY 4.0](LICENSE): you may reuse this text, change it and share it, as long as you credit the
source.
