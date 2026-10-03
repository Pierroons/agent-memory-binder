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
| **Work registry** | `<work>/inbox/work-in-progress.md` — what each session is doing now — only for parallel sessions | at start and after `/compact` |
| **Send log** | `<work>/inbox/sends-YYYY-MM.md` — one line per publication to a shared git branch | its last line, at start |
| **Scripts** | `<work>/scripts/` — the checker, the purge, the hooks, the send script | never |
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
   - Do several agent sessions work in parallel on this workspace? If yes, what are their names, and
     do they publish to a shared git branch? Which one?
   - On which date should the first memory review take place? It then comes back every two weeks.

## Step 2 — Working folders

1. Create `<work>/temp/`, `<work>/deliverables/`, `<work>/archive/` and `<work>/scripts/`, plus
   `<work>/inbox/` for parallel sessions. Write the purge delay to `<work>/purge-days`, a file
   holding only that number: the purge and the checker read it there.
2. Propose a weekly purge of `<work>/temp/`, as a cron job. It first sets aside git repositories: no
   pass touches them, and it lists them; then it deletes files older than the delay, and finally the
   empty folders under `temp/`, whatever their date — emptying a folder resets its date to now. It
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
metadata:
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
  Measure again every state in the notes you keep, and write what you measure, with its date; report
  to the human every state your measurement contradicts. When you cannot measure a state, keep the
  date the note already gives it, or write "date unknown, not verified". Write a lesson plainly.
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
- Distill a buffer as soon as the checker reports it, as a defect or a warning, without waiting for
  the review: lessons go into the note; states are measured again, then written with their date;
  finished work goes to the archive.
- A section that belongs to another session starts with `> Reserved: <session name>`. Leave it as it
  is.
- Deleting a buffer is the human's decision, once you have shown that its content is in the notes.

## Step 6 — Inboxes (parallel sessions only)

1. In `<work>/inbox/`, write a `README.md` that states the protocol below and lists the sessions.
2. Create one file per session: `for-<name>.md`.
3. Suggest to the human a way to name each session at launch, for example an environment variable:
   `SESSION_NAME=backend claude`. If the human uses Remote Control, suggest passing the same name to
   the bridge: `SESSION_NAME=backend claude --remote-control backend`. For a session already
   running, `/rename backend` sets that name as seen from the same machine and from others.
4. Propose a `SessionStart` hook without matcher, in `~/.claude/settings.json`, which covers every
   project. It prints the session name and the path of its inbox; without a name, or with a name
   that has no inbox, it says so and lists the inboxes. Claude Code adds a `SessionStart` hook's
   output to the context, at startup, on resume and after `/compact`.
5. A session has two peer names, both distinct from its inbox name. Sessions on other machines see
   it under the name passed to `--remote-control`, which survives restarts; without it, under a
   title that follows its task. Sessions on the same machine see it under a generated name, which
   can change during the session, even with `--remote-control`. `/rename` sets both for the current
   session. Its own `ListAgents` gives it only its local name. To write to a session, use the name
   YOUR `ListAgents` shows, or the `bridge:` ID of a message received from it, which goes stale when
   it restarts. On the first exchange, have it state its inbox name: a generated name can point to
   another session, elsewhere or later, and an accepted send does not prove it reached the right
   one. A session's bracketed reference in `ListAgents` survives its name changes; every session on
   one machine sees the same one for it, another machine sees a different one.

Every entry follows this format:

```markdown
## <title in one line>
> from <sender name> — <YYYY-MM-DD HH:MM>
> read by <recipient name> — <YYYY-MM-DD HH:MM> — ✅ done: <one sentence>

<what was measured, the command used, and what is already done>
```

Dates and times are in the machine's local time. The sender writes the title, the `from` line and
the body; the recipient adds the `read by` line. An entry starts at a `## ` heading outside code
fences and ends at the next one.

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

**States.** A state is a sentence that can become false while nobody edits the file (Step 3). An
entry that reports a state follows a closed circuit, so that no session keeps working on a fact
that another one has already fixed:

```markdown
## 📌 STATE <sender>-<MMDD>-<HHMM> — <title in one line>
> from <sender name> — <YYYY-MM-DD HH:MM> — return expected
> read by <recipient name> — <YYYY-MM-DD HH:MM> — ⏳ waiting: <what blocks it>
```

- The identifier is the sender's name followed by the month, day, hour and minute of the deposit.
  The sender writes `return expected` or `no return expected`.
- The recipient adds its `read by` line as soon as it reads the entry, as for any entry. Once the
  state is dealt with, it replaces `⏳ waiting: …` on that same line with
  `✅ done: <what it measured, the command, and when>`.
- When a return is expected, the recipient then writes at the end of the sender's inbox an entry
  titled `## ↩️ RETURN <identifier> — done`, with its `from` line and what it measured, and removes
  the state from its own inbox. The sender reads the return and removes it: nobody carries the
  topic any more.
- A lesson, a piece of information or an open question stays an ordinary entry. Only states follow
  this circuit, because only they become false on their own.

## Step 7 — Work registry and shared branch (parallel sessions only)

Inboxes carry what is finished. They do not say what another session is doing right now, nor when
it publishes: two sessions can fix the same thing on two branches, or push to the same branch a
minute apart. This step closes both gaps.

**The registry.**

1. Create `<work>/inbox/work-in-progress.md`, with the protocol below at its top and the block
   template inside a code fence. In the registry, every `## ` heading outside a code fence is a
   block: write the protocol without `## ` headings. The hook, the send script and the checker
   skip code fences.
2. Before choosing a topic, read the registry. Once the topic is chosen, and before you open the
   first file, add this block at the end of the registry:

```markdown
## 🔨 <session>-<MMDD>-<HHMM> — <what you are going to do, in one line>
> opened by <session name> — <YYYY-MM-DD HH:MM>
Repository: <name> · Branch: <the remote branch you will send to> · Target: <files or folders>
```

3. When the work is published, add one line under the block:
   `> ✅ closed — <YYYY-MM-DD HH:MM> — <commit SHAs>`.

The protocol:

- The description reserves the topic. A commit SHA exists only after the commit, too late for the
  session that needed to read you, and a rebase changes it: it goes on the closing line only.
- Add a block at the end of the file, and a line with one targeted insertion. Never rewrite the
  whole file: a rewrite erases the block another session has just added.
- The registry is a board, not a lock. When an open block overlaps your topic, tell the human
  before starting.
- Closed blocks move to `<work>/inbox/work-archive-YYYY-MM.md` at each promotion to the main
  branch, or at the review.

**One branch per session**, when the sessions share a git repository:

4. From a clone of the repository, give each session its own working tree and branch, started from
   the shared branch named at Step 1, here `dev`:
   `git fetch origin && git worktree add --no-track -b session/<name> <path> origin/dev`. Start
   from `origin/dev`: in a clone without a local `dev`, `… -b session/<name> <path> dev` makes git
   create a local `dev` and ignore `-b`, and the session works on `dev`. `--no-track` keeps a bare
   `git push` from aiming at `dev`.

**The send script.**

5. Write `<work>/scripts/send.sh <worktree> [--main]`. The human runs it; a session gives the
   human the command and never runs it. It reads the inbox folder from `SEND_INBOX`, by default
   `<work>/inbox`. The forge URL it serves is written once, at its top; it compares it with
   `git config remote.origin.url`, because `git remote get-url` applies `insteadOf` and returns
   another address. It runs git with `LC_ALL=C`, so that git's
   messages read the same on every machine. Without `--main`, it:
   - refuses a worktree with uncommitted changes, or one on `dev` or `main`, and stops without
     logging when the branch has nothing to send;
   - fetches, then merges `origin/dev` into the session branch when `dev` has moved. It never
     rebases. On a conflict, it aborts the merge, names the conflicting files and stops, with
     `dev` untouched;
   - pushes the session branch to `dev` and to itself in one atomic push (`git push --atomic`);
   - starts again from the fetch, up to three retries, when the push is refused because `dev` has
     moved in the meantime, and only then. Git reports that in two forms: `[rejected]` with
     `(fetch first)` or `(non-fast-forward)`; and, when two sends reach the forge at the same
     instant, `[remote rejected]` with `cannot lock ref` on a `remote:` line. Any other refusal,
     from a hook or from the forge, stops it;
   - checks after the push, with `git ls-remote`, that `dev` on the forge contains the sent commit;
   - appends one line to `<work>/inbox/sends-YYYY-MM.md`:
     `- YYYY-MM-DD HH:MM · dev · <short SHA> ← <branch> · <n> commit(s) · <subject>`, where `<n>`
     counts the commits new on `dev`, the merge included, and `<subject>` is the subject of the
     session's own last commit (`git log --first-parent --no-merges -1`). The line ends with
     `· retry` when it had to start again.

   With `--main`, it moves `main` to `dev` only as a fast-forward, once the forge's checks on `dev`
   have passed. It reads them with the forge's command line (for GitHub,
   `gh api repos/<owner>/<repo>/commits/<SHA>/check-runs`) every 30 seconds while they have not
   finished or not started, for 15 minutes at most. Then it moves the closed blocks of the
   registry to the archive of the month, rewriting the registry only if it has not changed since
   the script read it; otherwise the blocks wait for the review. It logs the line with `main` and
   `← dev`.
6. Write `<work>/scripts/send-bench.sh`. It fools the script from the outside only: a local bare
   repository stands for the forge (`git config url.<local path>.insteadOf <forge URL>`), a fake
   forge command line first in `PATH` returns the check results, `SEND_INBOX` points to a temporary
   inbox folder, and `GIT_CONFIG_GLOBAL=/dev/null` with `GIT_CONFIG_NOSYSTEM=1` keeps the human's
   git settings out. A fake `git` first in `PATH`, or a `reference-transaction` hook in the clone,
   lets another session push between a send's fetch and its push; a fake `sleep` keeps the waits
   of `--main` short. Cases:
   - two sends in the same second both pass, one of them logged with `· retry`; a `pre-receive`
     hook on the bare repository that waits two seconds makes them really overlap;
   - another session pushes between a send's fetch and its push: the send passes, with `· retry`;
   - a conflict stops with `dev` unchanged;
   - a `pre-push` hook that refuses stops the send without a retry;
   - failed checks block `--main`, and running then passing checks let it through;
   - a repository other than the configured one is refused.

   Mark the merge and the retry in `send.sh` between comment lines (`# [merge]` … `# [/merge]`,
   `# [retry]` … `# [/retry]`). At each run, the bench removes each marked region from a copy of
   the script, checks that lines were removed and that the copy passes `bash -n`, and verifies
   that it fails on that copy. Run it and show the human its output.

**The hook that keeps publishing in the human's hands.**

7. Propose a `PreToolUse` hook with the matcher `Bash`, in `~/.claude/settings.json`, that refuses
   `git push` and the send script when you run them, with a reason that tells you to give the
   command to the human. It reads the command with a tokenizer that respects quotes and
   redirections (`2>&1` separates nothing), and splits it into segments on `|`, `;`, `&`, `&&`,
   `||`, line breaks, subshells, `$( )` and backticks. In each segment, it skips variable
   assignments, shell keywords (`if`, `then`, `do`, `{`, `!` …), wrappers with their options and
   arguments (`env`, `command`, `exec`, `nohup`, `time`, `nice`, `timeout 30`, `sudo -u <user>`)
   and git's global options (`-C <path>`, `-c <key=value>`, `--git-dir=…`, `--work-tree=…`). It
   judges the name of the script given to `bash`, `sh` or `source`, without reading it, so that the
   bench stays allowed, and judges again the text given to `-c` or to `eval`; otherwise it judges
   the first word left. A search for the text `git push` is not a
   push. Test it on these commands before installing it:

| command | expected |
|---|---|
| `git push origin dev` | refused |
| `git -C ../repo push origin dev` | refused |
| `cd repo && git  push origin dev` (two spaces) | refused |
| `git --git-dir=/x/.git push origin dev` | refused |
| `<work>/scripts/send.sh ../repo` | refused |
| `bash <work>/scripts/send.sh ../repo` | refused |
| `sh -c 'git push origin dev'` | refused |
| `echo "$(git push origin dev)"` | refused |
| `timeout 30 git push origin dev` | refused |
| `bash <work>/scripts/send-bench.sh` | allowed |
| `grep -rn 'git push' docs/ 2>&1` | allowed |
| `echo 'do not run git push yourself'` | allowed |
| `git log --oneline origin/dev..HEAD` | allowed |

8. Extend the `SessionStart` hook of Step 6: after the inbox, it prints the open blocks of the
   registry and the last line of the most recent send log.

## Step 8 — The checker

Write `<work>/scripts/check-memory.sh` in bash, or in the language the human prefers. It takes the
memory folder as its argument, `--work <folder>` for the working folder, and `--inbox <folder>` for
the inbox folder, `<work>/inbox` by default; its report names the inbox folder it read. It reads the purge delay from `<work>/purge-days`. Links are `[[note-name]]` and
Markdown links to `.md` files; text inside backticks holds no link. It reports:

1. links that point to no file
2. notes linked neither from the index nor from a summary, outside `_buffer/`
3. an index over 150 lines or 20,000 bytes
4. notes without frontmatter or without `description`
5. buffers past their deadline, over 8 KB, or named outside the two patterns of Step 5
6. **Always** lines over 240 characters
7. files in `<work>/temp/` older than the purge delay plus 7 days, outside git repositories: the
   purge has stopped
8. when inboxes exist: entries without a `read by` line 24 hours after their `from` date,
   ordinary entries `⏳ waiting` whose `read by` line is older than 14 days, states still
   `⏳ waiting` 24 hours after their `read by` line, and entries whose `from` or `read by` line
   carries no readable date
9. when the registry exists: more than 12 open blocks, blocks open for more than 7 days, and open
   blocks without a readable date

When there are no inboxes, the report says so on one line: "inbox: none — check 8 not applicable".
Likewise for the registry: "registry: none — check 9 not applicable".

It also warns about buffers `<target-note>__YYYYMMDD.md` whose target note does not exist: the note
is still to be written, or the name is wrong. A warning is not a defect.

Exit code: 0 when nothing is found apart from warnings, 1 when something is found, 2 when
misconfigured. The report concludes "nothing to report" only with no defect and no warning.

Then add a `--canary` mode: copy the memory folder, `<work>/temp/` with its file dates,
`<work>/purge-days` and the inbox folder to a temporary directory that the script deletes when done;
run the checks on the copy; plant one defect per condition of each check; run the checks again;
verify that every planted defect appears in the second report and not in the first. Also plant, for
each exclusion — a link inside backticks, a git repository in `<work>/temp/`, an entry marked done,
a state marked done, a closed block older than 7 days, an entry deposited an hour ago and not read
yet, an ordinary entry waiting for three days, a `## ` heading inside a code fence — a case the checks must leave silent, and verify it
stays silent. Also build, in the same temporary directory, a minimal memory with its own working
folder — an index linking a single note, a buffer dated today for a missing note, an empty `temp/`
and the purge delay —: the checker must warn about it, must not conclude "nothing to report", and
must exit 0. At the end, verify that the temporary directory no longer exists. In this mode, exit 0
when every planted defect is reported, every silent case stays silent, the minimal memory gives
that result and the temporary directory is gone, 1 otherwise. Run the canary once now and show the
human its output. Then break one line of the checker, run the canary again, check that it exits 1,
and restore the line. A check you have never seen fail proves nothing.

## Step 9 — System note and report

1. Write `reference_memory_system.md`: the conventions the next sessions need — working folders and
   purge, note format, state and lesson, buffer naming and deadlines, archive naming, the path of
   the inbox `README.md`, which holds their format and protocol, the paths of the registry, the
   send script and its bench, how to run the checker and its canary.
2. Add the lines of **Every session** below to the **Always** section of the index, each linked to
   `reference_memory_system.md`.
3. Show the human what you created, merged, moved and left untouched, what is proposed for their
   decision, the checker output and the index size. Offer to delete `memory-before-setup/` once
   they are satisfied with the new memory.

## Every session

- **At session start and after every `/compact`, when inboxes exist** → read your inbox in full
- **Before working on a topic** → search the memory folder for it and read what you find
- **Before choosing a topic, when parallel sessions exist** → read the work registry, then add
  your block before opening the first file
- **To publish on the shared branch** → give the human the send command; never push yourself
- **When something is decided** → one line in the buffer
- **Before writing a state** → measure it now, and write it with its date
- **Before trusting a check that passes** → see it fail first on the defect it targets
- **On or after <review date>, with the human** → run the checker and its canary, distill the
  buffers, archive closed work, then set the next date two weeks later
