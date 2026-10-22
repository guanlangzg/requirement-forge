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

The file gets created *before* Q1 and updated after every answer — not written at the end. Conversation context gets compacted; the file is what survives.

Order is Goal → Acceptance → Boundary → Data/structure → Interaction → Wording, and there's an enforcement gate: if any of the first three is still open, the next question may only target an open one. Without that gate, user answers drag you topic-by-topic and Acceptance silently never gets asked.

**四 · Close** — Fill in the remaining sections, then a self-check: can every acceptance criterion be traced back to a `D`-numbered decision or the user's own words? Anything you invented gets marked as unconfirmed rather than blended in with what was agreed.

## Stop condition

Two hard signals come first, and the score only breaks ties:

1. **Has the gate been passed?** Goals, Acceptance and Boundaries each need a real `D`-numbered decision — a restatement of what the user said doesn't count. Not passed means keep asking, whatever the score says.
2. **Does every requirement survive the test?** If you can't write it as a checkable sentence, it isn't settled.

Only when both pass does the five-dimension score (Goals / Acceptance / Boundaries / Alternatives / Assumptions, 0–1 each) decide whether to close or run another round. Its job there is to surface a neglected dimension, not to be the verdict. The scale is banded, not measured — higher is clearer, and don't let a 0.60-vs-0.65 difference make the decision. Anything explicitly logged as open (with who decides and by when) doesn't count against you.

## Install

**As a ZCode project skill** — copy the directory into your project:

```bash
git clone https://github.com/guanlangzg/requirement-forge.git
cp -r requirement-forge ~/.agents/skills/         # user-level, all projects
```

**Pick one location, not both.** ZCode dedupes skills by *real path*, not by name — two copies at different paths both appear in the `$` menu as `requirement-forge`. If you need a project-local override, put it in `.zcode/skills/` instead; `.zcode` does shadow a same-named `.agents` copy.

**For other agents that read `SKILL.md`** — the format is the [Agent Skills](https://agentskills.io) standard. Drop the directory wherever that tool discovers skills. `~/.claude/skills/` for Claude Code.

**No install needed** — the whole thing is plain Markdown. Read `SKILL.md` and follow it, or paste it into any chat.

## What's inside

| File | Lines | Purpose |
| :--- | ---: | :--- |
| `SKILL.md` | 347 | The workflow. This is the skill. |
| `assets/需求文档模板.md` | 135 | 9-section output template + interview log (Chinese) |
| `references/question-bank.md` | 135 | Questions by task type + "anti-questions" for evasive answers |
| `DESIGN.md` | 407 | Every mechanism traced to its upstream source, what was rejected and why, and the revision log |
| `tests/README.md` | 171 | 8 real test cases across three rounds, including failures found |

## What it borrows and what it changes

`DESIGN.md` has 23 traced mechanisms. The short version:

- **Frontier discipline** from [mattpocock/skills](https://github.com/mattpocock/skills) `grilling` — but changed from "ask the whole frontier in one round" to "ask one." The discipline stays, the batching goes.
- **Three gates before any question** from [chenlu-hung/my-skills](https://github.com/chenlu-hung/my-skills).
- **Confidence numbers, and the "what would you actually want if you didn't have to justify it?" probe** from [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) `interview-me`.
- **Five-dimension ambiguity scoring** from [max4c/skills](https://github.com/max4c/skills) — three thresholds collapsed into one, and the scale flipped so higher means clearer.
- **Write-as-you-go** from [EricTechPro/grill-me-checkpoint](https://github.com/EricTechPro/grill-me-checkpoint).
- **"Silently deciding for the user is overreach; deciding in the open is diligence"** from [KKKKhazix/khazix-skills](https://github.com/KKKKhazix/khazix-skills) `leader`.
- **Questions must be full interrogatives with a plain-language "why it matters"** from [github/spec-kit](https://github.com/github/spec-kit) `clarify`.

Not borrowed: wayfinder, doubt-driven-development, discernment-nudge, and `to-questionnaire` — each solves a different problem; see `DESIGN.md` §2.

No upstream code was copied. Mechanisms and a few phrasing ideas were re-written.

## Known gaps

**The five-dimension score is the weakest part.** The one long-flow test that reached the exit gate landed 0.05 above the line, and rescoring the same session with an equally defensible reading flips the verdict. That's why the stop condition now leans on two checkable signals (gate passed, every requirement testable) and treats the mean as a second opinion. The scale still has no anchors; two people can disagree by 0.25 on the same session.

**The enforcement gate was validated by recovery, not prevention.** In testing, the out-of-order question had already been asked; the gate's fallback clause is what caught it. It should prevent drift when nobody is scripting the questions, but that's a design argument, not evidence.

**"Update the file after every answer" got violated in testing.** The rule is right; compliance is not automatic. Worth a harder checkpoint if it keeps slipping.


## Language

The skill body is in Chinese — it was written for a Chinese-speaking user and the questions it asks are shaped by that. The structure, gating logic, and scoring are language-independent; translating `SKILL.md` into English is a mechanical pass, not a redesign.

## License

MIT. See [LICENSE](./LICENSE).
