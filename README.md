# Requirement Forge

An agent skill that turns a messy, half-formed request into a document you can actually build from.

Most people don't describe what they want — they describe what they *think they should ask for*. "Build me a dashboard." "I want Redis." "Make it faster." The goal is buried inside the means, the scope is unstated, and success was never defined. Requirement Forge digs it out.

```
Messy request
     ↓
1. Restate    →  show your reading, name the contradictions and gaps
2. Infer      →  check reality, propose what to cut (no questions yet)
3. Interrogate →  one question at a time, each with a recommended answer
4. Write      →  produce the requirements doc, updating it as you go
```

## Why the first two stages don't ask questions

Every other interview skill starts asking immediately. This one deliberately doesn't. When someone has just dumped a pile of half-formed thoughts, firing questions at them is both annoying and inaccurate — they don't yet know how you've interpreted what they said.

So instead: give them a reading to react to. Reacting is faster than generating from scratch, and **correcting a specific wrong guess is cheaper than answering an abstract question**. A wrong reading is more useful than a neutral one, because it's easier to point at what's off.

By the time the questions start, every one of them lands.

## The four stages

**一 · Restate** — No questions. Output a structured read: core need (means stripped out), your guess at the real intent with a confidence number, what's already clear, contradictions side by side, and gaps. The gaps are *not* a question list — they're a statement of what you know you don't know.

**二 · Infer** — No questions. Actually go look: what exists in the project, what the environment allows, what's publicly verifiable. Then propose. At most three suggestions, each marked as a proposal you can reject. What to *cut* is worth more than what to add — scope creep is the most common failure at this stage.

**三 · Interrogate** — One question at a time, each with a recommended answer and a one-line "why this matters." Questions are ordered by a decision tree: only ask what sits on the frontier (prerequisites already settled). Before any question, three gates: will the answer change the outcome, can I look it up myself, is there an obvious default.

**四 · Write** — Fill in the template. Keep writing as you go, not at the end — context gets compacted, the file is the only reliable record.

## Stop condition

Not "enough questions asked" — "nothing left to guess." Five dimensions scored 0–1: Goals, Acceptance, Boundaries, Alternatives, Assumptions. Mean above 0.35 means keep going. Anything explicitly logged as open (with who decides and by when) doesn't count against you.

## Install

**As a ZCode project skill** — copy the directory into your project:

```bash
git clone https://github.com/guanlangzg/requirement-forge.git
cp -r requirement-forge ~/.zcode/skills/          # user-level, all projects
# or
cp -r requirement-forge <your-project>/.agents/skills/   # project-level
```

**For other agents that read `SKILL.md`** — the format is the [Agent Skills](https://agentskills.io) standard. Drop the directory wherever that tool discovers skills. `~/.claude/skills/` for Claude Code.

**No install needed** — the whole thing is plain Markdown. Read `SKILL.md` and follow it, or paste it into any chat.

## What's inside

| File | Lines | Purpose |
| :--- | ---: | :--- |
| `SKILL.md` | 274 | The workflow. This is the skill. |
| `assets/需求文档模板.md` | 102 | 9-section output template (Chinese) |
| `references/question-bank.md` | 115 | Questions by task type + "anti-questions" for evasive answers |
| `DESIGN.md` | 275 | Every mechanism traced to its upstream source, plus what was rejected and why |
| `tests/README.md` | 102 | 5 real test cases, two rounds |

## What it borrows and what it changes

`DESIGN.md` has 23 traced mechanisms. The short version:

- **Frontier discipline** from [mattpocock/skills](https://github.com/mattpocock/skills) `grilling` — but changed from "ask the whole frontier in one round" to "ask one." The discipline stays, the batching goes.
- **Three gates before any question** from [chenlu-hung/my-skills](https://github.com/chenlu-hung/my-skills).
- **Confidence numbers, and the "what would you actually want if you didn't have to justify it?" probe** from [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) `interview-me`.
- **Five-dimension ambiguity scoring** from [max4c/skills](https://github.com/max4c/skills) — three thresholds collapsed into one at 0.35.
- **Write-as-you-go** from [EricTechPro/grill-me-checkpoint](https://github.com/EricTechPro/grill-me-checkpoint).
- **"Silently deciding for the user is overreach; deciding in the open is diligence"** from [KKKKhazix/khazix-skills](https://github.com/KKKKhazix/khazix-skills) `leader`.
- **Questions must be full interrogatives with a plain-language "why it matters"** from [github/spec-kit](https://github.com/github/spec-kit) `clarify`.

Not borrowed: wayfinder, doubt-driven-development, discernment-nudge, and `to-questionnaire` — each solves a different problem; see `DESIGN.md` §2.

No upstream code was copied. Mechanisms and a few phrasing ideas were re-written.

## Known gap

The 0.35 ambiguity threshold has not been validated end-to-end. All five test cases stopped within the first two rounds, so the exit gate was never exercised. Long-term use is the only way to check it.

## Language

The skill body is in Chinese — it was written for a Chinese-speaking user and the questions it asks are shaped by that. The structure, gating logic, and scoring are language-independent; translating `SKILL.md` into English is a mechanical pass, not a redesign.

## License

MIT. See [LICENSE](./LICENSE).
