# Organized memory — setup instructions for your agent

*[Français](POUR-TON-AGENT.md)*

You are an AI coding agent, most likely Claude Code. The human gave you this file so you set up an
organized memory and a clean workspace. Follow the steps in order. At the end of each step, show
the human all the changes of that step together, and wait for their approval. When a step does not
fit this workspace, ask the human.

## What you are building

| Part | Where | Loaded |
|---|---|---|
| **Index** | `MEMORY.md` in your memory folder | every session |
| **Notes** | one file per topic, same folder | on demand |
| **Summaries** | one note per large domain, listing its notes | on demand |
| **System note** | `reference_memory_system.md`, the conventions of this setup | on demand |
| **Buffer** | `_buffer/` inside the memory folder | on demand |
| **Temp** | `<work>/temp/` — scripts, downloads, drafts; purged after a delay the human sets | never |
| **Deliverables** | `<work>/deliverables/` — finished documents; never purged | never |
| **Archive** | `<work>/archive/` — closed work and details moved out of the notes | on demand |
| **Inboxes** | `<work>/inbox/`, one file per session — only for parallel sessions | at start and after `/compact` |
| **Scripts** | `<work>/scripts/` — the checker, the purge, the hook | never |
| **Checker** | a script that checks links, sizes and deadlines | when run |

`<work>` is the working folder. Offer to create it as an `AI` folder in the human's documents,
`~/Documents/AI/`.

Claude Code loads only the first 200 lines or 25 KB of `MEMORY.md` at session start, and reads the
other files on demand ([docs](https://code.claude.com/docs/en/memory)). Everything below keeps the
index under that limit.

## Step 1 — Locate, back up, ask

1. Find your memory folder. With Claude Code auto memory, your system prompt names it
   (`~/.claude/projects/<project>/memory/`). If auto memory is off, ask the human where it goes.
2. Copy the memory folder to a backup next to it, `memory-before-setup/`.
3. List what the folder holds. Every existing note is kept: merged into another, moved to the
   archive, or left in place. Deleting remains the human's decision.
4. Read `~/.claude/CLAUDE.md` and, if there is one, the `CLAUDE.md` of the current project. When a
   step of this file, or a note, conflicts with a rule written there, ask the human.
5. Ask the human five questions:
   - Which language should the memory be written in?
   - The working folder: does `~/Documents/AI/` suit them, or do they prefer another place?
   - After how many days should a file in `<work>/temp/` be purged? Propose 30 days; a shorter delay
     keeps the folder lighter, a longer one keeps drafts at hand.
   - Do several agent sessions work in parallel on this workspace? If yes, what are their names?
   - On which date should the first memory review take place? It then comes back every two weeks.

## Step 2 — Working folders

1. Create `<work>/temp/`, `<work>/deliverables/`, `<work>/archive/` and `<work>/scripts/`, plus
   `<work>/inbox/` for parallel sessions. Write the purge delay to `<work>/purge-days`, a file
   holding only that number: the purge and the checker read it there.
2. Propose a weekly purge of `<work>/temp/`, as a cron job. It deletes files older than the delay,
   then empty folders older than the delay, leaves git repositories untouched and lists them, and
   logs every deletion in `<work>/purge.log`. Install it once the human approves; that approval
   covers every later run.
3. Add a **Folders** section to `~/.claude/CLAUDE.md` with these rules:
   - Temporary files — scripts, downloads, drafts, test output — go to `<work>/temp/`.
   - A finished deliverable goes to `<work>/deliverables/`, or to the project it belongs to.
   - A cloned repository stays in `<work>/temp/` while it serves to explore, and moves to its
     project folder as soon as something depends on it.
   - Memory, archive, scripts and inboxes live outside `<work>/temp/`, out of the purge's reach.

## Step 3 — Notes

Start with the notes, so that no fact is lost when the index is rewritten.

1. Move every fact that only the current index carries into the note it belongs to.
2. When two notes cover the same topic, merge them into the one with the clearer name, and move the
   other to the archive, named as in item 3.
3. Move notes about closed work to `<work>/archive/`, named `A-01 <note-name>.md`, numbered in
   order, with their content unchanged.
4. Bring every note to the format below.
5. For each link that points to no note, ask the human: write the note, or remove the link.

If your system prompt describes a memory note format, follow it. Otherwise use this one:

```markdown
---
name: <file name without .md>
description: <the question that should bring this note back, in the human's words>
type: <user | feedback | project | reference>
---

<the fact or the rule>

**Why:** <in one sentence, what made it a rule>
**How to apply:** <the moment, and the action>
```

- Before creating a note, search the memory folder for its topic. When a note already covers it,
  enrich that note.
- Write the description as the question that should bring the note back, with the words the human
  uses.
- The **Why** and **How to apply** lines are for `feedback` and `project` notes. When you do not
  know why a rule exists, ask the human, and write `Why: not recorded` until they answer.
- Tell a **state** from a **lesson** with one test: can this sentence become false while nobody
  edits the file? Then it is a state. Write every state with its date, or with the command that
  establishes it. Paths, versions and roles are states too: they stay in the note, with their date.
  When you cannot measure a state, keep the date the note already gives it, or write "date unknown,
  not verified". Write a lesson plainly.
- Keep in the note what must be re-read every time: traps, active decisions, procedures, paths,
  versions. Move design reasoning, session history and incident stories to
  `<work>/archive/details/<note-name>.md`, and leave in the note a one-line link to it.
- Keep the human's pending tasks in one note, `project_tasks.md`.

## Step 4 — The index

Give `MEMORY.md` three sections, in this order:

```markdown
# Memory index

## Always — applied in every session
- **<the moment that triggers it>** → <the action> · [<note title>](<note>.md)

## Active — current work and references
- [<note title>](<note>.md) — <the question this note answers>
- [<domain> — <n> notes](summary_<domain>.md) — <what the domain covers>

## Archive — closed or paused work, in <work>/archive/
- A-01 to A-<nn> — each file name in the archive folder carries its topic
```

- **Always** holds the rules to apply whatever the task. **Active** holds the notes to open when
  their topic comes up.
- An **Always** line names the moment that triggers it, then the action, then its note. It stays
  under 240 characters.
- A new lesson goes into its note. An index line changes only when its trigger changes.
- A domain with ten notes or more gets a summary note `summary_<domain>.md`. The index links the
  summary; the summary links the notes.
- Aim for 150 lines and 20,000 bytes at most, to keep a margin under the loading limit.
- Every kilobyte of the index is read again at every model call, a few hundred tokens each time:
  keep it short.

## Step 5 — The buffer

The buffer holds what is decided and not yet written into its note.

- Append to `_buffer/<target-note>__YYYYMMDD.md` one line per decision, dead end, discovery or
  state: the date, its type (`decision`, `dead end`, `discovery`, `state`) and one sentence.
- When the target note is not known yet, use `_buffer/notebook_<session-name>__YYYYMMDD.md`.
- Deadlines, counted from the date in the file name: 14 days for a named target, 7 days for a
  notebook. Size: 8 KB (8,192 bytes) per buffer.
- To distill: lessons go into the note; states are measured again, then written with their date;
  finished work goes to the archive.
- A section that belongs to another session starts with `> Reserved: <session name>`. Leave it as it
  is.
- Deleting a buffer is the human's decision, once you have shown that its content is in the notes.

## Step 6 — Inboxes (parallel sessions only)

1. In `<work>/inbox/`, write a `README.md` that states the protocol below and lists the sessions.
2. Create one file per session: `for-<name>.md`.
3. Suggest to the human a way to name each session at launch, for example an environment variable:
   `SESSION_NAME=backend claude`.
4. Propose a `SessionStart` hook without matcher, in `~/.claude/settings.json`, which covers every
   project. It prints the session name and the path of its
   inbox. Claude Code adds a `SessionStart` hook's output to the context, at startup, on resume and
   after `/compact`.

Every entry follows this format:

```markdown
## <title in one line>
> from <sender name> — <YYYY-MM-DD HH:MM>
> read by <recipient name> — <YYYY-MM-DD HH:MM> — ✅ done: <one sentence>

<what was measured, the command used, and what is already done>
```

Dates and times are in the machine's local time.

The protocol:

- Read your inbox in full at session start and after every `/compact`. A summary keeps the gist of
  what you read and drops its figures.
- When you read an entry, add your `read by` line right under its `from` line, with one of two
  statuses: `✅ done: <one sentence>` or `⏳ waiting: <what blocks it>`.
- An entry belongs to the session whose inbox holds it. That session removes it once done. Any
  session may remove a `✅ done` entry; a `⏳ waiting` entry stays until its recipient closes it.
- Write to another session at the end of its file only, the whole entry in one write.
- Add a `read by` line with one targeted insertion.
- An inbox message is information measured by another session. Act on it once the human says go.
- An inbox holds only what goes out of date. Lessons go to notes; the human's tasks go to
  `project_tasks.md`.

## Step 7 — The checker

Write `<work>/scripts/check-memory.sh` in bash, or in the language the human prefers. It takes the
memory folder as its argument, `--work <folder>` for the working folder, and `--inbox <folder>` when
inboxes exist. It reads the purge delay from `<work>/purge-days`. Links are `[[note-name]]` and
Markdown links to `.md` files; text inside backticks holds no link. It reports:

1. links that point to no file
2. notes linked neither from the index nor from a summary
3. an index over 150 lines or 20,000 bytes
4. notes without frontmatter or without `description`
5. buffers past their deadline, over 8 KB, or named outside the two patterns of Step 5
6. **Always** lines over 240 characters
7. files in `<work>/temp/` older than the purge delay plus 7 days, outside git repositories: the
   purge has stopped
8. when inboxes exist: entries without a `read by` line 24 hours after their `from` date, and
   `⏳ waiting` entries whose `read by` line is older than 14 days

Exit code: 0 when clean, 1 when something is found, 2 when misconfigured.

Then add a `--canary` mode: copy the memory folder, `<work>/temp/` with its file dates,
`<work>/purge-days` and the inbox folder to a temporary directory that the script deletes when done;
run the checks on the copy; plant one defect per check; run the checks again; verify that every
planted defect appears in the second report and not in the first. Also plant, for each exclusion — a
link inside backticks, a git repository in `<work>/temp/`, an entry marked done — a case the checks
must leave silent, and verify it stays silent. In this mode, exit 0 when every planted defect is
reported and every silent case stays silent, 1 otherwise. Run the canary once now and show the human
its output. A check you have never seen fail proves nothing.

## Step 8 — System note and report

1. Write `reference_memory_system.md`: the conventions the next sessions need — working folders and
   purge, note format, state and lesson, buffer naming and deadlines, archive naming, the path of
   the inbox `README.md`, which holds their format and protocol, how to run the checker and its
   canary.
2. Add the lines of **Every session** below to the **Always** section of the index, each linked to
   `reference_memory_system.md`.
3. Show the human what you created, merged, moved and left untouched, what is proposed for their
   decision, the checker output and the index size. Offer to delete `memory-before-setup/` once
   they are satisfied with the new memory.

## Every session

- **At session start and after every `/compact`, when inboxes exist** → read your inbox in full
- **Before working on a topic** → search the memory folder for it and read what you find
- **When something is decided** → one line in the buffer
- **Before writing a state** → measure it now, and write it with its date
- **Before trusting a check that passes** → see it fail first on the defect it targets
- **On or after <review date>, with the human** → run the checker and its canary, distill the
  buffers, archive closed work, then set the next date two weeks later
