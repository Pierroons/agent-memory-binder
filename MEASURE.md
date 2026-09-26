# Measuring what the memory costs

*[Français](MEASURE.fr.md)*

How we measured the memory on 26 September 2026, what we found, and how to run the same measurement
on your own setup. A single measurement on one memory: rerun it before you trust it.

## What is measured

| Quantity | Question |
|---|---|
| **Fixed cost** | How many tokens does the memory add to every call? |
| **Accuracy** | Does Claude find what only the memory knows? |
| **Cost to a complete answer** | How many tokens, and how many corrections, until the answer holds everything it should? |

A cheaper answer that is wrong or incomplete is not a saving. Every comparison below counts the
tokens **and** checks the answer.

## The two configurations

- **With memory**: a normal session.
- **Without memory**: `CLAUDE_CODE_DISABLE_AUTO_MEMORY=1`, plus a read ban on every folder of the
  memory system (the memory folder, the archive, the inboxes), for example
  `--disallowedTools 'Read(<memory folder>/**)'`.

In both: the same working directory and `CLAUDE.md`, the same model, read-only tools
(`--disallowedTools Write Edit NotebookEdit Bash Agent WebFetch WebSearch`), hooks off
(`--settings '{"disableAllHooks": true}'`), and a read ban on what would give the answer away
(the bench files themselves, the session transcripts `~/.claude/projects/**/*.jsonl`, and
`~/.claude/file-history/**`, where Claude Code keeps copies of the files it has edited, memory
notes included).

## Bench 1 — questions

Fifteen questions in three families, each asked three times per configuration, one fresh
`claude -p "<question>" --output-format json` session per run:

| Family | Where the answer is | What it measures |
|---|---|---|
| A | only in the memory | what is lost without memory |
| B | in `CLAUDE.md`, loaded in both | **the control**: both must succeed at the same cost |
| C | findable by searching the files | whether the memory shortens the search |

Each question has a reference answer and one to three keywords that a right answer must contain.
Alternate the order of the two configurations from one run to the next.

## Bench 2 — real tasks

Three tasks from daily work, each graded against a checklist of points a good answer must contain,
in three configurations: with memory, without memory but with the human's own short re-explanation
written beforehand, and without anything. Three runs each.

Grade **blind**: copy the answers under random names and have them scored by an agent that does not
know which configuration produced which answer, and that quotes the sentence proving each point.

## Bench 3 — corrections until complete

Resume each session of bench 2 (`claude -p "<correction>" --resume <session id>`). After each
answer, a judge lists the missing points; a correction naming them is sent back; repeat until
nothing is missing, three corrections at most. Count the whole conversation.

Validate the judge first against the blind grading. Keep it only where it agrees well **and** errs in
both directions: a judge that misses points in one configuration only sends it useless corrections
and tilts the result.

## Real conversation

The closest to real use: two interactive sessions on the same task, one with memory, one without.
The human converses as usual. Fix the stopping rule **before** starting: a written list of what the
finished result must contain, a neutral checker who answers only "reached" or "not reached" after
each answer, and a cap on the number of messages.

## Counting

- Take tokens from `modelUsage` in the JSON output (input, cache writes, cache reads, output), not
  from the transcript alone: an attempt that ended without an answer appears in the first and not
  always in the second.
- The JSON's `total_cost_usd` is a client-side estimate at list price.
- In an interactive session, sum the `usage` of each assistant message in the transcript, once per
  message id.

## Traps we hit

- **Model fallback.** A session may switch to another model mid-way (we saw safety refusals
  followed by a fallback). It then measures the fallback, not the memory: set it aside and run it
  again.
- **Leaks.** Read bans apply to Grep and Glob on a best-effort basis. Check the transcripts for any
  *successful* read of a banned path; a refused read is fine.
- **Learning.** The second of two real conversations benefits from the first. Swap the order on a
  second task, or use two different tasks of the same difficulty.
- **Perfect corrections.** Corrections written from the checklist are more precise than a human's,
  which favours the configuration without memory.
- **Never edit a bash script while it runs**: bash reads it as it goes.

## What we found (26 September 2026, one memory of about 200 notes)

| Measure | With memory | Without |
|---|---|---|
| Tokens added per call (one-step answers) | about +8,200 | — |
| Family A, right answers | 15 / 15 | 2 / 14 |
| Corrections needed, bench 3 (8 sessions per configuration) | 9 | 15 |
| Tokens to a complete answer, bench 3 | from 41% fewer to 36% more, depending on the task | — |
| Real conversation, tokens | 1,357,536 | 1,773,474 |

Limits: one memory, questions and tasks written by its owner, three runs per case, one real
conversation per configuration, and three biases that favour the memory in the real conversation
(order, the checklist pasted one message earlier, a single run).

## Next measurement

With a translator, a step that turns the request into clear instructions from the start, compare
with and without the memory: does a memory already in place use less than an instruction passed on
every time?
