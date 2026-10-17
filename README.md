# HATH /mcq

`/mcq` is a slash command that makes Claude ask before it builds. Type `/mcq` with any request and Claude checks its assumptions first, then asks multiple-choice questions you answer with a tap, shows you what it's about to produce, and waits for **Go**.

I built it because I didn't want to become a prompt engineer. My LLM could ask the questions a good prompt would have answered. I'd never hand a person a task without letting them ask questions, so I stopped doing it to AI.

## What's here

| File | What it is |
|---|---|
| [`mcq/SKILL.md`](mcq/SKILL.md) | The skill itself. Add it to Claude as a skill named `mcq`. |
| [`profile-rules.md`](profile-rules.md) | Rules to paste into your Claude profile so `/mcq` (and `/premises`) behave the way the skill expects. |

## How a session runs

1. **Premises.** Claude lists the few assumptions that would change the answer if they're wrong. You tap Correct, Wrong or Not sure.
2. **Rounds.** Multiple-choice tap questions until nothing structural is left to guess.
3. **Preview.** A numbered list of what it will build. You keep or drop items and pick the form and length.
4. **Go.** Nothing gets built until you tap Go. Typing `go` at any point skips ahead.

## What it's done for me

- I run it about 25 times a week across work and home (my estimate).
- Each one saves me roughly 15–30 minutes of back-and-forth corrections, so about 6–12 hours a week.
- After the questions, the first output is usable nearly every time. Before, it was about 1 in 3.

## Getting started

1. In Claude, add `mcq/SKILL.md` as a skill named `mcq`.
2. Paste `profile-rules.md` into your profile preferences.
3. Start any request with `/mcq` and answer the cards.

Part of HATH (Humanity at the Helm), my name for how I set up Claude: I design and decide, Claude builds.

## License

MIT. Free to use, copy, and change. See LICENSE.
