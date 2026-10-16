---
name: "mcq"
description: "Multiple Choice Clarification protocol — premises check, then unlimited tap-UI clarifying rounds until ambiguity is resolved, with contradiction detection, an output preview, and a tap-based go/no-go. Use whenever the user types /mcq, and auto-fire (one round) when a request has 2+ unresolved structural ambiguities and a wrong guess would cost real rework."
---

# MCQ — Multiple Choice Clarification Mode

Follow every rule exactly. No commentary outside this format.

## The tap tool

"Tap card" means the platform's tap-question tool: `ask_user_input` in claude.ai, `AskUserQuestion` in Claude Code. Every question in every round goes through it. Never fall back to plain-text questions.

## Render order — the rule everything else depends on

A tap card ends what the user can see. In claude.ai, nothing written after the call renders. So every response that contains a tap card is built in this order:

1. **Detail text** — anything the user needs to read to answer: premise explanations, element lists, assumptions, contradictions.
2. **Status line** — exactly one of the lines in Closing, as the last line of text.
3. **Tap card** — the call itself. Write nothing after it.

In Claude Code, the answers come back and the conversation continues in the same turn. Each new round starts over at step 1.

If a response has no tap card (a contradiction stop, a dead end, a framing exit), the status line is the last line of the response.

## Card labels

The tool allows 3 questions per call and 4 options per question. Option labels are short. They cannot carry explanations.

- **Anything that needs more than a short label** goes in the detail text as a numbered list. The card options then say only the number and a 2–5 word tag: `3 — go/no-go by tap`.
- **Every label must read one way on its own.** Never use options like "Both hold," "Neither," or "Yes" whose meaning depends on how the question is phrased. Say what the option asserts: "All correct," "#1 is wrong."
- **More than 3 questions** → sequential calls in the same round, with continuous numbering.
- **More than 4 options** → consolidate to 4 if they collapse honestly. Otherwise split into `N (1 of 2)` and `N (2 of 2)`, multi-select on both, and state in the first half's question that a pick in either half counts.
- Use multi-select wherever the options are not mutually exclusive.
- The tool supplies its own write-in. Never build one, and never let it take an option slot. The write-in is also the note field: a note that contradicts or qualifies a selection wins.

## Round 0 — Premises

Before any clarifying question, list the assumptions that would change the output if wrong. Use the user's /premises definition if one is loaded. Otherwise use this: at most 3, ranked by how much the output changes, and dropping anything that is obvious, verifiable with a tool, or cheap to fix afterward.

- **Detail text:** the numbered premises. For each: the assumption, whether it was stated or inferred, and what changes if it's wrong. Mark the one the output most depends on.
- **Tap card:** one multi-select question, "Which of these are wrong?" Options: `#1 wrong — [tag]`, `#2 wrong — [tag]`, … plus `All correct`. At most 3 premises, so this always fits in 4 slots.
- **One premise survives:** state it in the detail text and go straight to Round 1, with no premises card.
- **None survive:** write `Premises: none critical` on one line and start Round 1.

Round 0 is not numbered. Draft Round 1 only after the user answers. Their corrections override the task framing.

## Rounds

Rounds continue without limit until every structural ambiguity is resolved or stated as an assumption. There is no threshold question.

Round 1 begins at question 1. Numbering continues across rounds and never restarts. Maximum 5 questions per round.

## Output preview and go/no-go

The last round before output is the preview round. It combines up to three questions in one card:

**Elements** — ask this unless the contents have already been explicitly settled, not merely implied.
- **Detail text:** a numbered list of what the output will contain, one element per line, each ≤80 characters. This is where the user reads the detail. The card only points to it.
- **Card:** multi-select, "Which elements should be included?" One option per element, as `N — tag`. Split into halves per Card labels if there are more than 4.
- **Reading answers:** a selected element is included and an unselected one is dropped. The write-in adds or changes elements. A skipped Elements question means all elements are included.

**Form and length** — ask this unless it's already settled. Offer all four options with nothing pre-selected: Prose / Bullets or structured / Table / Mixed — your call. Give length as a word band plus a structural description: `~200 words, a paragraph` / `~600 words, about a screen` / `~1,500 words, about a page` / `as long as it takes`. Never offer character counts.

**Go/no-go** — always the last question of the preview round. Single-select, "Ready to proceed?", with the options `Go — build it` and `Another round`.
- **Go:** apply all Elements and Form answers and any write-in corrections, then produce the output immediately. Corrections combined with Go do not trigger another round unless they create a contradiction.
- **Another round:** use the write-in, if any, to draft the next round. Then show the preview again.

Never call any of these questions a gate.

**If the card holds more than 3 questions** (Elements split in two, plus Form, plus Go), put Elements and Form in the first call and Go alone in the second. Go is always the last card.

## Typed go (backup)

A message consisting only of `go` or `x` (case-insensitive, punctuation ignored) at any point stops rounds and starts output. Use every answer so far, and state any remaining ambiguity as an assumption before the output. Never show this as an option or announce it. It exists so the user can cut rounds short without waiting for the preview.

In Claude Code, a leading slash gets caught by the command parser and never reaches the model. So `/go` is accepted if it arrives, but when telling the user what to type, always write `go`.

## Interruptions

- **Contradiction** — an answer conflicts with a prior answer or with the task framing. Stop, with no card: `Contradiction detected: [Answer X] conflicts with [prior answer or task framing]. Please resolve before I continue.`
- **Skipped questions** — state an assumption for each in the next response's detail text: `Skipped [#] — assuming [X].` Allow correction before going deep.
- **"Wrong" or "reread"** — reprocess the previous answer and confirm the reprocessing before continuing.
- **"I don't understand the question"** (in a write-in or message) — the question was badly built. Rewrite it with its detail moved into the detail text, and re-ask it in the next card.
- **Framing questioned** — exit MCQ, answer in prose, and offer to resume.
- **Repeated go** — two go signals, or the user asking why output hasn't started: begin output immediately.

## Closing

Dead end (contradictory answers, a task that structurally resists MCQ, or no convergence after 5+ rounds): say so, state assumptions, and offer to proceed or reset, in prose with no card.

The status line goes where Render order says. It must be exactly one of:

- `SUFFICIENT CONTEXT OBTAINED`
- `SUFFICIENT CONTEXT OBTAINED — ASSUMING [assumption]`
- `MORE CONTEXT NEEDED`

Before any tap call, check that the status line is written. This is the rule most often dropped. On the preview round, put this line directly above the status line: "I believe I have sufficient clarity to proceed."

## Auto-fire

Without `/mcq`, activate only when all three hold: 2+ unresolved structural ambiguities, a wrong guess would cost real rework, and clarification would resolve more than half the ambiguity. Auto-fire is limited to one round, because unlimited rounds on a protocol the user didn't ask for is friction, not clarity.

On auto-fire:
- Fold the premises question into the single round as one of its questions.
- Skip Form and length and Go/no-go. When the user answers the round, that answer is the go signal.

An explicit `/mcq` overrides every skip condition and restores unlimited rounds.
