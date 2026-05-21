---
name: journal
description: Activate session journaling for long-running, multi-session projects. Use when the user says "journal", "start journaling", "read the journal", "update the journal", "checkpoint", or at the start of any session where a journal.md already exists in the project. This skill manages the full lifecycle — initialization, orientation, mid-session checkpoints, decision recording, pre-compaction preservation, and end-of-session handoff.
---

# Session Journal

## What this is

A shared, append-only project journal that serves as the **only persistent memory** between sessions. Without it, every new session starts blind. The journal captures decisions, discoveries, dead ends, and context that cannot be derived from code or git history alone.

It is not a transcript. It is not a changelog. It is a curated record of **why** things happened, written so that a future session — with zero prior context — can orient itself and continue the work.

**Announce at start:** "I'm using the journal skill to [initialize / orient from / update] the project journal."

---

## The Golden Rule

> The journal is our only long-running memory. Without making changes there, we are absolutely blind going into the next session.

Update the journal at every natural breakpoint — not batched at the end. If a decision was made, a problem was hit, or direction changed, it goes in the journal **now**.

---

## Lifecycle

When the skill is invoked, determine which phase applies:

- **No journal exists** → Initialize (section 1)
- **Journal exists, start of session** → Orient (section 2)
- **Journal exists, mid-session** → Checkpoint (section 3)

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
2. Read this journal end-to-end before touching code
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

## Session Log

<Append-only. New entries go at the bottom. Never edit previous entries — record corrections as new entries.>
```

#### Recommended Sequence (extreme cases only)

For sweeping, multi-week refactors that touch most of the codebase — the kind of work that spans multiple sprints and dozens of sessions — a separate `journal-recommended-sequence.md` may be warranted. **Do not create this by default.** Only propose it when:

- The change is so large that the journal's session log alone cannot keep the agent oriented in the noise
- There is a known, ordered sequence of steps that must be followed across many sessions
- Acceptance criteria for the overall effort need a single place to live

The sequence document is a **high-level roadmap**: where we are headed, what the acceptance criteria are at the end, and how we get there step by step. It is not a task list — it's a compass. The journal tracks what happened; the sequence tracks where we're going. Link them to each other.

When a sequence document exists, sessions should cross-reference it to mark steps as done, struck (moot), or reframed — but the sequence is the user's decision to create, not something the agent proposes lightly.

**Splitting journals by phase/step:** When a sequence document exists and the work spans many sessions, the journal can be split into multiple files — one per major phase or step of the sequence. This prevents any single file from growing unwieldy:

```
docs/journals/billing-rebuild/
├── journal.md                         ← header sections only (principles, ubiquitous language, how we work together)
├── journal-recommended-sequence.md    ← the compass
├── journal-step-1.2-design.md         ← session log for step 1.2
├── journal-step-1.3-design.md         ← session log for step 1.3
└── journal-step-3.1.md                ← session log for step 3.1
```

In this structure, `journal.md` retains the evergreen header sections (How We Work Together, Ubiquitous Language, Founding Principles, Working Notes) and links to the per-step journals. Each step journal contains only its own session log entries. The sequence document tells you which step journal to read for orientation.

### 2. Orient (journal exists, new session)

This is the most critical phase. Before writing any code:

1. **Read the journal in full.** Use `grep '^## \|^### ' journal.md` first to understand the document's structure, then read it end-to-end. Don't skip sections — a decision encoded in an earlier entry may still be load-bearing, and struck items tell you what was tried and abandoned. If the journal has been split into per-step files, read `journal.md` (the header) in full, then read the step journal indicated by the sequence document or the most recent entry's "Next" pointer.

2. **Read companion documents** if they exist:
   - `journal-recommended-sequence.md` — only exists for sweeping multi-week refactors; if present, skim for current step and what's struck/reframed
   - Any linked design docs or specs referenced in the journal

3. **Check recent git history and cross-reference:**

   ```
   git log --oneline -15
   ```

   Cross-reference against the journal. If commits exist that aren't reflected in the journal, flag this — the journal may be stale.

4. **Identify KEY PATTERN callouts.** If the journal flags critical architectural knowledge (e.g., "always read X before touching Y"), surface these prominently in your orientation summary.

5. **Note HANDS OFF items.** If the journal or opening prompt marks certain files, tasks, or areas as off-limits ("I'm handling those in a separate session"), respect these boundaries for the entire session.

6. **Summarize your orientation** to the user in 3-5 sentences: what you understand the current state to be, what was deferred, and what seems like the natural next step. Then **ask** what they'd like to work on — do not assume.

### 3. Mid-Session Checkpoints

Append a checkpoint entry at natural breakpoints:

- A feature or sub-task is complete
- A significant decision was made
- A problem was discovered that changes direction
- The user asks for a journal update
- Before switching to a different area of work
- An inflection point — the kind of moment where you'd say "this changes things"

**Session numbering:** determine the session number by counting existing session entries in the journal. If this is the first entry, it's Session 1. Checkpoint numbering (K) resets per session.

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

After writing a checkpoint, review the last 2-5 commits (`git log --oneline -5`) and verify they're all reflected. If any are missing, add them to the entry before moving on.

Do **not** add timestamps to individual items unless the user specifically asks for them.

### 4. Decision Recording

When a decision is made during discussion:

- Record it in the current checkpoint under "Decisions"
- If an alternative was considered and rejected, note it: "Option B (rejected): ... — recording for future discussion"
- If a decision contradicts or supersedes an earlier one, add a correction entry — do **not** edit the original

For architectural or foundational decisions that affect the whole project, also consider whether they belong in the "Founding Principles" section at the top.

### 5. Pre-Compaction (mid-session, context filling up)

This is a mid-stream event — we're still working, but context is running low.

**Before generating prompts:** verify the journal is up-to-date. If recent work, decisions, or discoveries haven't been checkpointed yet, write a quick checkpoint first. The journal must reflect the current state before compaction — anything not in the journal is lost.

Generate **two ephemeral prompts** for the user:

**Prompt 1 — for `/compact`:** Tells the compaction engine what to preserve. This is based on what the agent anticipates doing next — the plan we've laid out, the next step in the sequence, or what the user has indicated:

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
1. `<path/to/journal.md>` — read in full (overview, "How We Work Together", ubiquitous language, founding principles, session log).
2. `git log --oneline -15` on current branch.
3. <if sequence doc exists:> `journal-recommended-sequence.md` — focus on Steps X, Y, Z.
4. <if other linked docs:> list them with what to focus on.

CURRENT BRANCH STATE (for orientation, not as source of truth):
- Branch: <branch-name> (N commits ahead of <base>)
- HEAD: <short sha + message>
- Recent work: <1-2 sentence summary>
- HANDS OFF: <items the user is handling separately, if any>

REFOCUS DIRECTIVE: <if applicable — why a fresh start was needed, e.g., "drifted into side quests, return to Step N">

KEY PATTERN: <if applicable — critical architectural knowledge that must be re-read before touching certain areas>

FIRST TASK: <what to pick up>
```

### 6. End-of-Session Close

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

### 7. Side Quests

For tangential work that isn't part of the main task:

- Keep the entry brief — a few sentences, not a full checkpoint
- Label it clearly as a side quest or tangent
- Do **not** invent step numbers from the main sequence
- Link back to the main work: "Returning to Step N after this tangent"

### 8. Branch Cleanup (Pre-Merge)

Before merging a feature branch back to the base branch, the journal files are removed from the tree. This is always the last step:

```bash
git rm --cached journal*.md
# or if under docs/journals/<feature>/
git rm --cached docs/journals/<feature>/journal*.md
```

Commit the removal. The journal content is preserved in git history but doesn't clutter the main branch.

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

## Rules

1. **Entries are append-only and chronological.** Never delete or rewrite previous entries — the thinking trail matters. When something turns out to be wrong or no longer applies, use **strikethrough** (`~~...~~`) on the original text, add a brief inline correction with a date, and expand on the correction in a new entry further down. This preserves the visible record of how thinking evolved:

   ```markdown
   ~~Step 4.2: Add per-region fallback routing to the ingestion pipeline.~~

   _(2026-04-16) Struck — moot after discovering the gateway already handles this. See Session 8 entry below for the investigation._
   ```

   A reader skimming the journal should be able to see: here was the original thinking, here's where it was corrected, and here's the full explanation. Never just yank something — the course correction is as valuable as the original decision.

2. **Journal is separate from living documentation.** Living docs (design specs, API docs) reflect the codebase AS-IS today. The journal reflects WHAT HAPPENED and WHY. Don't conflate them.
3. **The journal must stay accurate.** Strikethrough applies to decisions, reasoning, and narrative — the thinking trail. But mechanical references (renamed tables, moved files, changed function signatures) are different — leaving a stale file path in the journal doesn't preserve useful history, it creates traps. Fix these in place with a sed pass and note the update in the session log ("Updated stale references: renamed X to Y across journal").
4. **Don't let the journal get too long without structure.** If it exceeds ~500 lines, consider splitting. For sequence-driven projects, split by step/phase (one journal file per major step). For simpler projects, keep the header sections in the main journal and move older session entries to an archive file. Propose the split to the user — don't do it unilaterally.
5. **Companion documents are linked, not inlined.** Design specs, sequence docs, and investigation notes live in their own files. The journal links to them and records the decisions that came out of them.
6. **Every journal entry should be self-contained enough that a reader skimming just that entry understands what happened.** Don't write "continued from above" — restate enough context.

---

## Multi-Journal Projects

For large projects with multiple concurrent workstreams, organize journals under `docs/journals/<feature>/`:

```
docs/journals/
├── billing-rebuild/
│   ├── journal.md
│   └── journal-recommended-sequence.md   ← only for sweeping multi-week refactors
├── api-migration/
│   └── journal.md
└── auth-overhaul/
    └── journal.md
```

Each journal is self-contained with its own header, principles, and session log. Cross-reference between journals when work in one affects another.

Most features will have only `journal.md`. The sequence document is the exception, not the norm — reserved for when you know upfront that the work will span weeks and touch most of the codebase.
