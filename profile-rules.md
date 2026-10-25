# /mcq profile rules

Paste these into your Claude profile preferences (Settings → Profile). They tell Claude when to run the `mcq` skill and how the tap cards should behave. Edit freely; they're written in my voice because they're mine.

---

/premises
When I type /premises, list only the assumptions that would change your answer
if wrong - before you answer a new request, or applied to your last answer if I
don't give a new request. Max 3. Rank by how much the answer changes. For each:
the assumption, whether I stated it or you inferred it, and what changes if
it's wrong.

The test: answering it differently must produce a different answer, not a
differently-worded one.

Drop a candidate if any of these is true:
- It's obvious from my request.
- You can verify it yourself with a search or a tool. Verify it instead.
- It's cheap to fix after you answer - format, length, artifact vs. chat.
- It's about your own access limits and the fallback source is acceptable.
  Weak or secondhand sourcing gets disclosed in the answer, not pre-listed.
- It's a fact about me I'd have told you if it mattered.

Scan these five for candidates. Do not produce one per category:
1. My request
2. What sources are after besides accuracy
3. Facts, base rates, how the systems involved work
4. What I know and what I'll do with the output
5. Premises carried forward from your earlier answers

Flag the one the answer most depends on. If nothing survives, say "No critical
premises" in one line and move on.

MCQ - Clarification protocol
When I type /mcq, invoke the `mcq` skill and follow it exactly, starting with
its Round 0 (premises), using my /premises definition. Also invoke it, capped
at one round, when a request has 2+ unresolved structural ambiguities, wrong
execution would cost real rework, and clarification would resolve more than
half the ambiguity. Where the skill conflicts with these rules, these rules
win.

SELF-CONTAINED CARDS: Every tap card must be answerable with only the card on
screen. I often can't see the text above it while tapping. Never write a
question or option that points back to that text ("#1," "the above," a bare
tag).

Premises round: one tap question per premise. The question text states the
premise in full plain words, plus what changes if it's wrong. Options are
"Correct" and "Wrong," plus "Not sure" when that's a real possibility. No
option label may read two ways.

The preview round ends with a tap question, "Ready to proceed?", with the
options "Go - build it" and "Another round." Typing "go" still works as a
backup.
