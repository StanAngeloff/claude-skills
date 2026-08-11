---
name: journal
description: Activate session journaling for long-running, multi-session projects. Use when the user says "journal", "start journaling", "read the journal", "update the journal", "checkpoint", "vacuum the journal", "audit the journal", "what's still open", "are we done", or at the start of any session where a journal.md already exists in the project. This skill manages the full lifecycle — initialization, orientation, mid-session checkpoints, decision recording, journal vacuuming, on-request audits, pre-compaction preservation, and end-of-session handoff.
---

# Session Journal

## What this is

A shared project journal that is the **single home for the work's state** between sessions. Without it, every new session starts blind. The journal captures decisions, discoveries, dead ends, and context that cannot be derived from code or git history alone. Where the harness also keeps a persistent auto-memory, memory points at the journal — it never mirrors it (see "Where State Lives").

It is not a transcript. It is not a changelog. It is a curated record of **why** things happened, written so that a future session — with zero prior context — can orient itself and continue the work.

**Announce at start:** "I'm using the journal skill to [initialize / orient from / update] the project journal."

---

## The Golden Rule

> The journal is the only long-running memory of the work. Without making changes there, we are absolutely blind going into the next session.

Update the journal at every natural breakpoint — not batched at the end. If a decision was made, a problem was hit, or direction changed, it goes in the journal **now**.

---

## Lifecycle

When the skill is invoked, determine which phase applies:

- **No journal exists** → Initialize (section 1)
- **Journal exists, start of session** → Orient (section 2)
- **Journal exists, mid-session** → Checkpoint (section 3)
- **Live Session Log past its budget** → Vacuum (section 5)
- **User asks what's still open or whether the work is done** → Audit (section 6)

### 1. Initialize (no journal exists yet)

If no `journal.md` exists in the project root (or `docs/journals/<feature>/journal.md` for feature branches), create one using the template below.

Ask the user:

- What is the project/feature about? (one paragraph of business context)
- What branch and base are we working on?
- Are there related issues, PRs, or discussions to link?

Then write the journal with these sections in order:

```markdown
# <Project or Feature Name> — Project Journal

**Started:** <YYYY-MM-DD>
**Status:** In progress
**Trigger:** <why this work exists — business context in one sentence>
**Branch:** `<branch-name>`
**Base:** `<base-branch>`
**Related:** <links to issues, PRs, discussions if any>

### Session setup

To resume in a new session:

1. Activate the `/journal` skill — it governs how we work with this document
2. Orient from this journal: header + Current State + the latest session by default, end-to-end only while the file is small
3. Check `git log --oneline -15` for recent commits
4. Read any companion documents linked below

---

## Why this journal exists

<One paragraph: what problem we're solving, why it matters, and why a journal is necessary (multi-session work, context exceeds a single conversation, etc.)>

---

## How We Work Together

This project is a collaboration. Every decision, direction, and plan is made together — never unilaterally.

1. **No solo planning.** Do not create roadmaps, phase plans, task lists, or directional decisions without discussing them with the user first. Present options, raise questions, surface trade-offs — then the user decides.
2. **Ask, don't assume.** When uncertain about direction, scope, priority, or approach — ask. The cost of a question is near zero.
3. **Show work incrementally.** After completing each discrete piece of work, stop and show it for review before moving on.
4. **The journal records what we agreed, not what one side decided.** Every entry should reflect a conversation, not a unilateral conclusion.
5. **Journal upkeep is pre-authorized.** Refreshing Current State and vacuuming the log past its budget are standing maintenance: do them at natural breakpoints and announce them in one line afterwards. Everything above needs discussion; the upkeep of this file does not.

---

## Ubiquitous Language

| Term | Definition |
| ---- | ---------- |
| ...  | ...        |

<Populate as domain terms emerge. Keep this table current.>

---

## Founding Principles

<Non-negotiable rules for this project. Numbered, with rationale. Add as they are established through discussion. Principles can be struck and corrected in place here — having current and superseded constraints visible together is more useful than scattering corrections across session entries. The session log should still get an entry explaining WHY a principle was struck.>

---

## Working Notes

<Practical tips for sessions: local DB access, sandbox workarounds, test commands, environment quirks. Keep this section up to date.>

---

## Current State

<Rewritten in place at every checkpoint — never appended, budget ~150 lines. Together with the sections above this is the default orientation read, so it must always answer: where the work stands, what is outstanding, and which entries, companions, or archives are worth opening for more. Closed items are deleted here — their history lives in the Session Log.>

**Standing:** <2-5 present-tense sentences: what is built, what is in review, what is blocked>

**Outstanding:** <numbered list of open items, each with an owner>

**Key patterns:** <only the facts a session must re-read before touching the areas they concern>

**Read further:** <companions and archives, each with a one-line "open when ..." trigger — omit until any exist>

---

## Session Log

<Append-only. New entries go at the bottom. Never edit previous entries — record corrections as new entries.>
```

### 2. Orient (journal exists, new session)

This is the most critical phase. Before writing any code:

1. **Read the header and Current State first.** Use `grep '^## \|^### ' journal.md` to map the structure, then size the read to the file. Under ~400 lines, read the journal end-to-end — don't skip sections; a decision encoded in an earlier entry may still be load-bearing, and struck items tell you what was tried and abandoned. Past that size, the default orientation read is the header sections, Current State, and the most recent session's entries; open older entries, archives, or companions only where Current State, the task, or a "Read further" trigger points. A full read of everything is the deliberate exception — name its cost before starting one; on a long-lived journal it dominates the session's token budget and can exceed what a file-read tool returns at all.

2. **Read companion documents** if they exist:
   - Any linked design docs or specs referenced in the journal
   - `archive/` files are history moved out by vacuuming, not orientation reading — open one only when the index or the task points into it

3. **Check recent git history and cross-reference:**

   ```
   git log --oneline -15
   ```

   Cross-reference against the journal. If commits exist that aren't reflected in the journal, flag this — the journal may be stale.

4. **Identify KEY PATTERN callouts.** If the journal flags critical architectural knowledge (e.g., "always read X before touching Y"), surface these prominently in your orientation summary.

5. **Note HANDS OFF items.** If the journal or opening prompt marks certain files, tasks, or areas as off-limits ("I'm handling those in a separate session"), respect these boundaries for the entire session.

6. **Summarize your orientation** to the user in 3-5 sentences: what you understand the current state to be, what was deferred, and what seems like the natural next step. If the opening prompt already pinned the first task, start it; otherwise **ask** what they'd like to work on — do not assume.

### 3. Mid-Session Checkpoints

Append a checkpoint entry at natural breakpoints:

- A feature or sub-task is complete
- A significant decision was made
- A problem was discovered that changes direction
- The user asks for a journal update
- Before switching to a different area of work
- An inflection point — the kind of moment where you'd say "this changes things"

**Session numbering:** the next number after the most recent session entry — check the archive index too right after a vacuum. If this is the first entry, it's Session 1. A session ends at a handoff (end of day, or the user handing the work off), **not** at compaction: mid-session compaction continues the same session and its checkpoint numbering. Checkpoint numbering (K) resets per session.

**Checkpoint format** (include only the sub-sections that have content — omit empty ones):

```markdown
### YYYY-MM-DD — Session N, Checkpoint K: <short title>

**Status:** <in progress / completed / blocked>

<Narrative: what happened, decisions made, problems hit, how they were solved.
Write for a future reader with zero context.>

#### Discoveries

- <non-obvious findings that a future session needs to know>

#### Decisions

- <what was decided and WHY — not just "chose X" but "chose X over Y because Z">

#### Key Patterns

- <critical architectural knowledge a future session must re-read before touching certain areas — e.g., "always check X before modifying Y">

#### Deferred

- <anything punted, with enough context to pick it up later>
```

**Before appending, read the log's tail.** If the last entry is one this session didn't write, or the Session/Checkpoint number is already taken, take the next free number and say so in the entry. Parallel sessions on one journal are normal; silent numbering collisions are not — a duplicate header costs a manual reconciliation entry later.

After writing a checkpoint, review the last 2-5 commits (`git log --oneline -5`) and verify they're all reflected. If any are missing, add them to the entry before moving on. Then refresh Current State so the two never disagree. A checkpoint is complete without any write to auto-memory — see "Where State Lives".

Do **not** add timestamps to individual items unless the user specifically asks for them.

### 4. Decision Recording

When a decision is made during discussion:

- Record it in the current checkpoint under "Decisions"
- If an alternative was considered and rejected, note it: "Option B (rejected): ... — recording for future discussion"
- If a decision contradicts or supersedes an earlier one, add a correction entry — do **not** edit the original

For architectural or foundational decisions that affect the whole project, also consider whether they belong in the "Founding Principles" section at the top.

### 5. Vacuum (pre-authorized size maintenance)

The live journal must stay loadable in one read. Past **~500 lines of Session Log or ~100KB of file**, vacuum at the next natural breakpoint. Authorization is standing (agreed at initialization under How We Work Together): do not queue a question behind it, announce it in one line afterwards — a mid-flow question stalls while the file keeps growing.

1. **Refresh Current State first**, promoting still-load-bearing Key Patterns and open items out of the entries about to move.
2. **Move whole closed sessions verbatim** into `archive/sessions-NN-MM.md`, or `archive/<phase>.md` when the moved sessions form a recognizable phase — everything except the running session and the most recent closed one. Archives are frozen once written: never edited, never required orientation reading.
3. **Maintain an index table in the main journal**: archive file → sessions → dates → one-line topics. Later entries cite archived material through it, and orientation uses it to decide whether an archive is worth opening.
4. **The trigger re-arms.** Vacuum again every time the live log regrows past the budget — a one-shot split only resets the clock.

A dense single day blows the budget as readily as a long-running effort, which is why the trigger is size, not elapsed time or phase. When work is genuinely enumerable upfront, the ordered step list lives in Current State under Outstanding, or in a companion document (rule 5) when it needs room — the journal itself grows and vacuums the same way either way.

### 6. Audit (on request)

When the user asks whether the work is done, what is still open or deferred, or whether anything was dropped — deliver this audit. It is an on-request operation only: never run or offer it unprompted.

The ledger is the thing under audit, so verdicts come from the entries. Deferrals are born inside session entries and only summarized in Current State; a build-heavy stretch drifts the summary first, and vacuuming takes old entries out of casual view. Certainty requires going back to the frozen record.

1. **Read everything**: `journal.md` end to end, every `archive/` file, every companion document, and `git log` since the journal's start date to catch closures no entry recorded. This is the one operation where the full read is the point. Archives can go to parallel subagent readers when large, with verdicts kept in the main session.
2. **Extract every open loop from the entries, not the ledger**: Deferred subsections, decisions awaiting a call, "waiting on" items and their owners, promised follow-ups, delegations to people or tickets, struck items whose correction implies replacement work.
3. **Verdict each with provenance**: CLOSED (cite the closing entry, commit, PR or ticket), OPEN-ON-LEDGER (present under Outstanding), OPEN-MISSING (open but absent from the ledger), DELEGATED-NO-LANDING (handed off with no evidence the receiving side has it).
4. **Report the delta, then repair**: deliver what is missing from the ledger, what the ledger lists that is actually closed, and every delegation without a landing site — quoting the originating entry for each, never a blanket all-clear. Then fold the confirmed verdicts into Current State as standing maintenance, and leave anything unprovable flagged for the user's call.

### 7. Pre-Compaction (mid-session, context filling up)

This is a mid-stream event — we're still working, but context is running low.

**Before generating prompts:** verify the journal is up-to-date. If recent work, decisions, or discoveries haven't been checkpointed yet, write a quick checkpoint first. The journal must reflect the current state before compaction — anything not in the journal is lost.

Generate **two ephemeral prompts** for the user:

**Prompt 1 — for `/compact`:** Tells the compaction engine what to preserve. This is based on what the agent anticipates doing next — the plan we've laid out, the next Outstanding item in Current State, or what the user has indicated:

```
Preserve context for continuing <next task description>. Key items:
- Current branch: <branch>, base: <base>
- Journal location: <path/to/journal.md>
- Last completed: <what was just finished>
- Next task: <what to pick up>
- Critical context: <any non-obvious state, key patterns, or gotchas that would be expensive to re-derive>
Discard: <side quests, exploration tangents, dead ends already captured in the journal>
```

**Prompt 2 — for resumption after compaction:** This is the verbose opening prompt that re-orients the compacted session. It should be detailed enough that the agent can continue without re-asking questions:

```
New session on <branch> (base: <base-branch>). You have no prior context.
Read canonical sources first, then we work.

STEP 0 — ORIENTATION READS (in order):
1. `<path/to/journal.md>` — header + Current State + the latest session's entries; full read only if the file is small or this prompt says so.
2. `git log --oneline -15` on current branch.
3. <if other linked docs:> list them with what to focus on.

CURRENT BRANCH STATE (for orientation, not as source of truth):
- Branch: <branch-name> (N commits ahead of <base>)
- HEAD: <short sha + message>
- Recent work: <1-2 sentence summary>
- HANDS OFF: <items the user is handling separately, if any>

REFOCUS DIRECTIVE: <if applicable — why a fresh start was needed, e.g., "drifted into side quests, return to Step N">

KEY PATTERN: <if applicable — critical architectural knowledge that must be re-read before touching certain areas>

FIRST TASK: <what to pick up>
```

Compaction is not a session boundary: the resumed conversation continues the same Session N, and "(closing)" stays reserved for a true end of session.

### 8. End-of-Session Close

When the user is done for the day — work is committed, the branch is clean, and they're shutting down. The next session will be a fresh start (new day, new agent, no shared context). The journal IS the handoff — no ephemeral prompts are needed.

Write a closing journal entry that contains everything a brand-new session needs to orient itself just by reading the journal:

```markdown
### YYYY-MM-DD — Session N (closing): <summary of session>

**Status:** <status>

#### Done this session

- <bullet list of what was accomplished>

#### Deferred

- <what was punted with context>

#### Next

- <what the next session should start with>
- <any open questions that need answering>

#### Context for next session

<A paragraph of narrative context that a fresh session needs. Include gotchas, things that almost went wrong, non-obvious state that the codebase doesn't make clear. Write this so that tomorrow's agent — pointed at the journal with "where do we stand?" — has everything it needs.>
```

A close-out entry is also required whenever the work changes state — merged, parked, reversed, superseded — no matter what kind of session lands it. Endgame sessions (review, CI, merge) are exactly where entries get skipped and journals freeze before the finale. The cheap form is fine — a one-line Status plus a Current State refresh beats a missing entry.

### 9. Side Quests

For tangential work that isn't part of the main task:

- Keep the entry brief — a few sentences, not a full checkpoint
- Label it clearly as a side quest or tangent
- Do **not** number it as part of the main line of work
- Link back to the main work: "Returning to the main thread after this tangent"

### 10. Worktrees

The journal never lives inside a worktree: worktree removal would take it along, and journal files are gitignored, so removal safety checks never notice them. Its real home is either the main checkout's `docs/journals/<feature>` or, when the project's conventions name one, a journal directory outside every checkout — check the journal's own Working Notes and the project's kickoff skill. The external home is the stronger arrangement wherever sessions run under a write-isolation guard, because it stays writable from everywhere.

When work moves into a git worktree, link the journal into it as part of worktree setup:

```bash
mkdir -p <worktree>/docs/journals
ln -s <main-checkout>/docs/journals/<feature> <worktree>/docs/journals/<feature>
```

The link is for reading: orientation and re-reads work from the worktree cwd through any link shape. Checkpoint writes go to the journal's real path directly — a write-isolation guard judges a write by where it lands and refuses worktree-side links that lead out of the worktree, whatever their target. When the real path itself is refused because the journal is resident in the guarded main checkout, exit the worktree, checkpoint from the root, and re-enter.

Never copy journal files into a worktree and never hardlink them: a copy forks the record immediately, a hardlink forks it on the first rename-style write. One real directory, reached by symlink for reads and by its own path for writes, is what keeps the record single.

### 11. Branch Cleanup (Pre-Merge)

Before merging a feature branch back to the base branch, the journal files are removed from the tree. This is always the last step:

```bash
git rm --cached journal*.md
# or if under docs/journals/<feature>/
git rm --cached docs/journals/<feature>/journal*.md
```

Commit the removal. The journal content is preserved in git history but doesn't clutter the main branch. If the journal directory is gitignored and was never tracked, there is nothing to remove and this step does not apply.

---

## What to Record

| Category                  | Examples                                               |
| ------------------------- | ------------------------------------------------------ |
| Decisions made            | "Chose X over Y because Z"                             |
| Decisions deferred        | "Punted on X — needs Y first"                          |
| Problems encountered      | "X didn't work because Y"                              |
| Workarounds applied       | "Had to use X instead of Y due to Z"                   |
| Design deviations         | "Design says X but we did Y because Z"                 |
| Things that broke         | "X stopped working after Y"                            |
| API/library surprises     | "API requires X before Y — not documented"             |
| Reframings                | "We thought X but discovered Y — changes everything"   |
| Course corrections        | Strike the original, date the correction, expand below |
| Phase/milestone progress  | "Step N complete", "Phase M started"                   |
| Working style corrections | "User wants X approach, not Y"                         |

## What NOT to Record

- **Implementation details obvious from reading the code.** If `git show` explains it, the journal doesn't need to.
- **Routine commits or file changes.** Git log handles that.
- **Copy-pasted error messages without context.** The error itself is noise — record what it _meant_ and what you did about it.
- **Timestamps on every item.** Only timestamp when the user asks for it.
- **Excessive detail on minor work.** The journal tracks top-level implementation. If an entry is getting too long, summarize in the journal and move detail to a linked document.
- **Re-pasted ledgers.** Don't re-list the outstanding-items table in entries as items close — Current State holds the live list once; entries record only the delta.

## Where State Lives

The journal owns the work's state. When the harness also keeps a persistent auto-memory (memory files plus an index loaded into every session), the boundary is:

- **The work's state** — status, decisions, open items, branch/PR facts — lives in the journal **only**. Never mirror it into memory files: a mirrored file goes stale within days, never gets read back for orientation, and costs a double write at every checkpoint — a pure tax while it lives and a trap for whoever finally reads it.
- **The memory index carries one line per effort**: its name plus "READ <path-to-journal>". Refresh that line's few words at session close; it holds no state of its own. This line is load-bearing — it is how a fresh session discovers the journal exists — which is exactly why it must stay a pointer.
- **Durable cross-project facts** — who the user is, standing feedback on how to work, reference knowledge that outlives any one effort — belong in auto-memory, not the journal. When session work produces one, record the promotion in the checkpoint ("promoted to memory: <name>") instead of duplicating its content in both places.
- **Narrate every memory write in chat as it happens.** Silent memory writes are how shadow copies form and persist unnoticed.

An effort does not need a ticket to deserve a journal: skill-building, investigations, and migrations get one under the same root. Without a journal, an initiative's state has nowhere durable to live except memory — and the shadow-copy problem returns by structural necessity.

## Rules

1. **Session Log entries are append-only and chronological.** Never delete or rewrite previous entries — the thinking trail matters. When something turns out to be wrong or no longer applies, use **strikethrough** (`~~...~~`) on the original text, add a brief inline correction with a date, and expand on the correction in a new entry further down. This preserves the visible record of how thinking evolved:

   ```markdown
   ~~Step 4.2: Add per-region fallback routing to the ingestion pipeline.~~

   _(2026-04-16) Struck — moot after discovering the gateway already handles this. See Session 8 entry below for the investigation._
   ```

   A reader skimming the journal should be able to see: here was the original thinking, here's where it was corrected, and here's the full explanation. Never just yank something — the course correction is as valuable as the original decision.

2. **Journal is separate from living documentation.** Living docs (design specs, API docs) reflect the codebase AS-IS today. The journal reflects WHAT HAPPENED and WHY. Don't conflate them.
3. **The journal must stay accurate.** Strikethrough applies to decisions, reasoning, and narrative — the thinking trail. But mechanical references (renamed tables, moved files, changed function signatures) are different — leaving a stale file path in the journal doesn't preserve useful history, it creates traps. Fix these in place with a sed pass and note the update in the session log ("Updated stale references: renamed X to Y across journal").
4. **Don't let the live journal outgrow a single read.** Vacuuming (section 5) is standing, pre-authorized maintenance: past the budget, do it at the next natural breakpoint and say so — don't queue a question. Current State and the archive index are the deliberate exceptions to rule 1: rewrite those in place and let the log carry the history.
5. **Companion documents are linked, not inlined.** Design specs, roadmaps, and investigation notes live in their own files. The journal links to them and records the decisions that came out of them.
6. **Every journal entry should be self-contained enough that a reader skimming just that entry understands what happened.** Don't write "continued from above" — restate enough context.

---

## Multi-Journal Projects

For large projects with multiple concurrent workstreams, organize journals under `docs/journals/<feature>/` — ticketed work and un-ticketed initiatives alike:

```
docs/journals/
├── billing-rebuild/
│   ├── journal.md
│   └── archive/                          ← appears once vacuuming starts
├── api-migration/
│   └── journal.md
└── auth-overhaul/
    └── journal.md
```

Each journal is self-contained with its own header, principles, and session log. Cross-reference between journals when work in one affects another.

Most features will have only `journal.md`, gaining an `archive/` once vacuuming starts and companion documents as the work warrants.
